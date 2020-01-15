# week2  
## dalgona  
* 8086 Memory Architecture  
  8086 시스템의 메모리 구조는 시스템이 초기화 시작 시 커널을 메모리에 적재하고 가용 메모리 영역(available space)를 확인하게 된다. 시스템은 운영에 필요한 기본적인
  명령어 집합을 커널에서 찾는다. 따라서 커널 영역은 항상 저 위치에 있다. 기본적으로 커널은 64kbyte를 차지하나 오늘날의 경우에는 더 큰 영역을
  차지한다. 32bit 시스템의 경우 메모리 영역에 주소를 할당할 수 있는 범위는 0 ~ 2^32-1이다. 64bit 시스템의 경우 메모리 영역은
  0 ~ 2^64-1이다.  
  운영체제는 하나의 프로세스 실행 시 이를 segment 단위로 묶어 가용 메모리 영역에 저장시킨다. 현재의 시스템은 멀티 
  테스킹이 가능하므로 메모리에는 여러 개의 프로세스가 저장되어 병렬적으로 작업을 수행한다. 따라서 여러 개의 segment들이 
  가용 메모리 영역에 저장 가능하다. 프로세스의 실행 시점에 메모리의 어느 위치에 저장될 지가 결정된다. 하나의 segment는 
  code segment, data segment, stack segment로 구성된다. 시스템에는 크기와 타입이 다양한 최대 16383개의 segment가 생성 가능하다.
  하나의 segment는 최대 2^32 byte의 크기를 가질 수 있다.  
  code segment: 시스템이 알아들을 수 있는 명령어 즉 instructio이 들어 있음  
  instruction: 이것은 기계어 코드로써 컴파일러가 만들어낸 코드이다. 명령을 수행하며 많은 분기 과정과 점프, 시스템 호출 등을 수행하게 된다. 이때 메모리 상의 위치에 있는 명령을 지정해주어야 한다. 그러나 segment는 위에서 말했듯이 실행 시점에 메모리 저장 위치가 결정되기에 컴파일 과정에서는 어느 위치에 저장될 지 모른다. 따라서 정확한 주소를 지정해줄 수 없다. 그래서 segment는 logical address를 사용한다.  
  logical address: 실제 메모리 상의 주소(physical address)와 매핑되어 있다. segment는 segment selector를 통해 자신의 시작 위치(offset)을 먼저 찾고 시작 위치로부터의 위치(logical address)에 있는 명령을 수행할 지를 결정하게 되는 것이다.  
  ~~~
  __physical address == offset + logical address__  
  ~~~
  따라서 segment가 메모리상의 어느 위치에 있더라도 segment selcector가 segment의 offset을 알아내어 해당 instruction의 정확한 위치를 찾아낼 수 있게 된다.  
  data segment: 프로그램이 실행시에 사용되는 데이터(전역 변수이다.)가 들어간다. 프로그램 내에 전역 변수 선언 시 그 변수가 data segment에 자리잡게 되는 것이다.  
  data segment==현재 모듈의 data structure+상위 레벨로부터 받는 데이터 모듈+동적 생성 데이터+다른 프로그램과 공유하는 공유 데이터  
  stack segment: 현재 수행되고 있는 handler, task, program이 저장되는 데이터 영역. 우리가 사용하는 버퍼(buffer)가 이 영역에 저장된다. 프로그램이 사용하는 multiple 스텍을 생성 가능하고 스텍간의 switch가 가능. 지역 변수들이 자리 잡는 공간.   
  스텍: 스텍은 처음 생성될 때 필요한 크기가 정해져 만들어진다. 프로세스의 명령에 의해 데이터를 저장해 나가는 과정을 거친다. 이때 stack pointer(sp)라고 하는 레지스터가 스텍의 맨 꼭대기를 가리키고 있다. 스택의 데이터를 저장 또는 읽어 들이는 과정은 PUSH와 POP instruction(명령어)에 의해서 수행된다. 스텍은 쌓여있는 접시와 같이 가장 최근에 PUSH된 데이터를 POP 명령을 통해 가져온다.  
* 8086 CPU 레지스터 구조  
  CPU가 프로세스를 실행하기 위해선 프로세스를 CPU에 적재시켜야 한다. 흩어져 있는 명령어 집합(instruction set)과 데이터들을 적절히 읽고 저장하기 위해서도 여러 가지 저장 공간이 필요하다. CPU가 빠른 속도로 읽고 쓰는 데이터들이므로 CPU 내부 메모리(레지스터)를 사용한다.  
  레지스터의 구성  
  범용 레지스터(General Purpose regitster): 논리 연산, 수리 연산에 사용되는 피연산자, 주소를 계산하는 피연산자, 그리고 메모리 포인터가 저장되는 레지스터. 프로그래머가 임의로 조작가능하게 허용된 레지스터. 일종의 4개의 32bit 변수. EAX,EBX,ECX,EDX 이 네가지는 프로그래머의 필요에 따라 사용해도 되지만 밑의 목적에 따라 사용해주는 것이 좋다.  
  ~~~
  EAX-피연산자와 연산 결과의 저장소  
  EBX-DS segment안의 데이터를 가리키는 포인터  
  ECX-문자열 처리나 루프를 위한 카운터  
  EDX-I/O 포인터  
  ESI- DS 레지스터가 기리키는 data segment 내의 어느 어느 데이터를 가리키고 있는 포인터. 문자열 처리에서 source를 가리킴  
  EDI- ES 레지스터가 가리키고 있는 data segment 내의 어느 데이터를 가리키고 있는 포인터. 문자열 처리에서 destination을 가리킴  
  ESP-SS 레지스터가 가리키는 stack segment의 맨 꼭대기를 가리키는 포인터  
  EBP-SS 레지스터가 가리키는 스텍 상의 한 데이터를 가리키는 포인터 
  ~~~
  세그먼트 레지스터(segment regitster): 세가지 세그먼트(code, data, stack)을 가리키는 주소가 들어있는 레지스터.프로세스의 특정 세그먼트를 가리키는 포인터 역할. CS레지스터는 code segment, DS,ES,FS,GS 레지스터는 data segment를, SS 레지스터는 stack segment를 가리킨다. 이들은 가리키는 위치를 바탕으로 segment 안의 특정 데이터,명령어를 정확하게 끄집어내는 것이 가능하다.  
  플래그 레지스터(program status and control register): 프로그램의 현재 상태나 조건 드을 검사하는데 사용되는 플래그들이 있는 레지스터. 상태 플래그, 컨트롤 플래그, 시스템 플래그들의 집합. 시스템이 리셋되어 초기화되면 이 레지스터는 0x00000002의 값을 가진다. 또한 1,3,5,15,22~31번 비트는 예약되어 소프트웨어를 통해 조작불가하다.  
  ~~~
  __status flags__  
  CF-carry flag. 연산 수행 중 carry 혹은 borrow가 발생하면 1이 된다. carry는 덧셈 연산 시 bit bound를 넘어가는 것이고 borrow는 뺄셈을 하는데 빌려오는 것을 얘기한다.  
  PF-Parity flag. 연산 결과 최하위 바이트의 값이 1인데 짝수인 경우 1이 된다. 패리티 체크를 하는데 사용.  
  AF-Adjust flag. 연산 결과 carry나 borrow가 3bit 이상 발생할 경우 1이 된다.  
  ZF-Zero flag.결과가 zero임을 가리킨다. if문 같은 조건문이 만족될 경우 1이 된다.  
  SF-sign flag. 연산 결과 최상위 비트의 값과 같다. 값이 양수이면 0, 음수이면 1이 된다.  
  OF-Overflow flag. 정수형 결과값이 너무 큰 양수거나 너무 작은 음수여서 피연산자의 데이터 타입이 모두 들어가지 않을 경우 1이 된다.  
  DF-Direction flag. 문자열 처리 시 1일 경우 문자열 instruction이 자동으로 감소(문자열 처리가 high address -> low address), 0일 경우 자동으로 증가 
  ~~~
  ~~~
  __system flags__  
  IF- Interrupt enable flag. 프로세서에게 mask한 interrupt에 응답할 수 있게 하려면 1을 준다.  
  TF -Trap flag. 디버깅을 할 때 single-step을 가능하게 하려면 1을 준다.  
  IOPL-I/O privilege level field. 현재 수행 중인 프로세스 혹은 task의 권한 레벨을 가리킨다. 현재 수행 중인 프로세스를 가리키는 CPL이 I/O address 영역에 접근하기 위해서는 IOPL보다 작거나 같아야 한다.  
  NT-Nested task flag. interrupt의 chain을 제어. 1이 되면 이전 실행 task와 현재 task가 연결되어 있음을 의미한다.  
  RF-Resume flag.Exception debug 하기 위해 프로세서의 응답을 제어  
  VM-Virtual-8086 mode flag. Virtual-8086 모드를 사용하려면 1을 준다  
  AC-Alignment check flag 이 비트와 CR0 레지스터의 AM 비트가 set되어 있으면 메모리 레퍼런스의 ac가 가능하다.  
  VIF-Virtual interrupt flag. IF flag의 가상 이미지. VIP flag와 결합시켜 사용  
  VIP-Virtual interrupt pending flag. 인터럽트가 pending(경쟁상태)되었음을 의미  
  ID-Identification flag. CPUID instruction을 지원하는 CPU인지 나타낸다. 
  ~~~
  인스트력션 포인터 (instruction pointer): 다음 수행해야할 명령(instruction)이 있는 메모리 주소가 들어가 있는 레지스터. 다음 실행할 명령어가 있는 code segment의 offset 값을 가진다. 하나의 명령어 범위에서 선형 명령 집합의 다음 위치를 가리킬 수 있다. 또한 JMP,Jcc,CALL,RET와 IRET instruction이 있는 주소값을 가진다. EIP 레지스터는 소프트웨어에 의해 바로 엑세스 불가하다. 그리고 control-transfer instruction(JMP,Jcc,CALL,RET)이나 interrupt와 exception에 의해서 제어된다. EIP 레지스터를 읽는 방법은 CALL instruction 수행 후 프로시저 스텍(procedure stack)으로부터 리턴하는 instruction의 address를 읽는 것이다. 프로시저 스텍의 return instruction pointer의 값을 수정하고 return instruction(RET,IRET)을 수행하여 EIP 레지스터의 값을 간접적으로 지정할 수 있다.  
* 프로그램 구동시 segment에서는 어떤 일이?  
  프로그램을 구동 시 어떤 일이 일어나는지 알기 위해 간단한 C 코드를 짠다. gcc -S-o 옵션을 이용하여 C코드를 컴파일한 어셈블리 코드를 만들어낸다. c 프로그램이 컴파일 되어 실제 메모리 상 어느 위치에 존재하는지 알기 위해 컴파일을 한 다음 gdb를  이용해 어셈블리 코드와 메모리에 적재될 logical address를 확인한다. gdb를 열었을 때 가장 앞에 붙어 있는 주소는 logical address이다. 이 주소를 통해 main 함수 내의 함수들은 아래에 자리 잡고 main()함수는 위에 자리잡은 것을 알 수 있다. segment의 logical address는 0x08000000부터 시작하지만 실제 프로그램이 컴파일과 링크되는 과정에서 다른 라이브러리들을 필요로 하게 된다. 따라서 코딩한 코드가 시작되는 지점은 시작점과 일치하지 않는다. 또한 stack segment도 0xBFFFFFFF까지 할당되지만 필요한 환경 변수 실행 옵션 주어진 변수 등등에 의해서 가용 영역은 그보다 아래에 자리잡고 있다. 우리가 짠 C프로그램은 전역변수를 지정하지 않기에 data segment에는 라이브러리의 전역변수만 들어있을 것이다. 여기서부터는 segment의 형태를 단계별로 묘사를 하지 못하기에 달고나문서를 참고하기 바란다.  
  
  
