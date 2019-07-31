#week1  
##technote  
###the_basics_technic_of_shellcode  
shellcode    
	-기계어로 작성된 작은 크기의 프로그램  
	-shellcode== 어셈으로 작성 후 -> 기계어 변경
	-실행중인 프로그램에 삽입된 코드
	-shellcode != 실행 가능 프로그램 -> 메모리 상 배치와 메모리 세그먼트 등 무관
C -> ASM -> 기계어
	-프로그램들 == 고수준의 언어(C...) --(컴파일)-> assembly code(저수준 언어) -> 기계어로 표현
	-기계어는 메모리에 로드되어 실행됨
	-따라서 공격자는 취약성을 통해 shell을 획득하기 위해 저수준 개발된 코드(shellcode) 필요
ASM 명령어
	mov [목표] , [소스]		:목표 피연산자 -(복사)-> 소스 피연산자
	push [값]			:stack에 값을 저장
	pop [레지스터]		:stack의 탑의 값 -(저장)-> 레지스터
	call [함수명([명령어 주소])]	:리턴을 위해 call 다음 실행될 명령어의 주소를 스택에 저장 -> 함수의 위치로 점프
	ret			:스택에서 리턴할 주소를 pop -> 그 주소로 점프하여 함수로부터 리턴
	inc [목표]			:목표 피연산자를 1 증가
	dec [목표]		:목표 피연산자를 1 감소
	add [목표],[값]		:목표 피연산자에 값을 더함
	sub [목표],[값]		:목표 피연산자에 값을 뺌
	or [목표],[값]		:목표 피연산자와 값 사이에 or 논리연산 -> 결과를 목표 피연산자에 저장
	and [목표],[값]		:목표 피연산자와 값 사이에 or 논리연산 -> 결과를 목표 피연산자에 저장
	xor [목표],[값]		:목표 피연산자와 값 사이에 xor 논리연산 -> 결과를 목표 피연산자에 저장
	lea [목표],[소스]		:목표 피연산자에 소스 피연산자의 유효 주소 로드
시스템 함수 호출 명령어
	-int(interrupt의 약자) 명령어의 피연산자 값으로 0x80을 전달 시 eax에 저장된 시스템 함수 호출
	-syscall(system call의 약자) 명령어 호출 시 rax에 저장된 시스템 함수 호출
linux system call in assembly
	-shellcode 개발위해 assemblycode에서 사용가능한 system 함수 필요
	-c에서는 표준 라이브러리 제공 -> 라이브러리가 다양한 아키텍처에 맞는 시스템 콜을 알기에 여러 시스템에서 컴파일 가능
	-어셈블리어는 특정 프로세서 아키텍처용이 정해져 있음 -> 호환 가능한 라이브러리 없음, 커널 시스템 직접 호출 가능
	-해당 파일에서 사용가능한 system 함수 확인 가능(system call 정보는 운영체제 ,아키텍쳐마다 다름)
	-리눅스 시스템 함수 이름과 call 번호가 나열되어 있음
	-어셈블리어로 시스템 함수 호출 시 시스템 콜 번호 사용
	-ubuntu 32bit와 64비트도 시스템 함수 콜번호 다름
save argument value in registers
	-system call 번호는 eax,rax에 저장
	-kernel은 cdecl함수 호출 규약을 기본으로 "system v abi"calling convention 도 사용
	-shellcode 작성 시 "system v abi" calling convention 사용
			register(32bit)		register(64bit)
	system call 	eax			rax
	argument1	ebx			rdi
	argument2	ecx			rsi
	argument3	edx			rdx
	argument4 	esi			r10
	argument5	edi			r8	
	argument6 	ebp			r9
assembly code 작성
	-어셈블리 코드는 앞부분에 메모르 세그먼트 선언
		ex) 데이터 세그먼트에 hello world 문자열과 개행 문자(0x0a)가 있음
	-텍스트 세그먼트에 실제 어셈블리어 명령이 있음
		-elf 바이너리를 생성하기 위해 global_start 줄(어셈블리 명령이 어디서부터 시작하는지 표시)가 필요
	-f elf 인자를 nasm 어셈블리를 사용해 helloworld.asm을 어셈블 -> asm 파일을 elf 바이너리로 링크가능한 목적(object)파일로 만듬
	-링커 프로그램 ld: 어셈블된 obj 파일 -(생성)-> 실행 가능한 a.out 바이너리
	-64비트의 경우는 32비트와 레지스터와 콜 번호가 다름
change to shellcode format
	-어셈블리 코드를 독자적으로 동작 가능하게 변경하여 shellcode화 가능
		-텍스트, 데이터 세그먼트를 사용하지 않음
		-함수의 호출은 call 명령어 사용
		-write 시스템 함수에 의해 출력될 문자열을 명령어 뒤에 작성
	-문제 발생
		-write 시스템 함수에 메시지가 저장된 주소를 전달해야 함
		-그러나 데이터 세그먼트를 사용하지 않는 assembly code의 특성상 mov 명령어로 값을 전달할 수 없음
	-문제 해결 (call, ret 명령어 사용)
		-call 명령어에 의해 함수 호출 시 call 명령어 다음 명령의 주소를 stack에 저장(push)
		-함수 사용 종료 시 ret 명령어를 통해 stack에 저장된 주소를 eip 레지스터에 저장
		-이러한 구조에 의해 ret 명령어 실행 전에 stack에 저장된 return 주소를 변경 시 실행 흐름을 변경 가능
		-따라서 call 명령어 뒤에 출력할 메시지를 저장시면 pop 명령어를 이용해 ecx 레지스터에 주소 전달 가능
	-메모리 세그먼트 사용하지 않음과 동시에 독자적으로 동작하는 code 생성완료
shellcode가 정상적으로 실행되지 않는 이유
	-shellcode에 nell byte(0x00) 때문
	-문자열을 다루는 함수들은 null byte를 만나면 문자열의 끝이라 판단
	-따라서 shellcode의 내용이 code의 영역에 복사되지 않아 문제 발생
	-null byte를 제거해야 함
call 명령어에 nullbyte 존재
	-call 명령어의 피연산자가 사용 가능한 값의 크기 == dword(32bits)
	-0x14와 같이 작은 값이 사용되면 남은 부분에 null byte 포함
call 명령어에서 null 제거 방법
	-jmp short last; 명령어를 통해 helloworld 함수를 우선 지나 last 함수로 먼저 이동
	-last 함수에서 helloworld 함수를 다시 호출해 , 출력할 메시지를 저장
null byte가 제거된 이유
	-jmp short 명령어의 특징 
		-jmp short 명령어는 0x20 byte로 이동
		-jmp 명령어의 피연산자의 사용가능한 값의 크기는 dword(32bits)
		-jmp short 명령어의 피연산자의 사용가능한 값의 크기는 -128 ~ 127
	-call 명령어는 helloworld 함수 호출위해 -0x22(0x2 - 0x24)로 이동해야 함
	-여기서 -0x22를 표현위해 2의 보수 즉 0xFFFFFFDD로 표현
	-따라서 jmp short와 call 음수 명령어에 의해 null byte 제거
register의 값을 저장하는 부분에 nullbyte 존재
	-64 bit,32bit,16bit 레지스터에 표현 가능한 값보다 작은 값을 저장 시 나머지 공간은 null byte로 채워짐
	64bit	RAX	RBX	RCX	RDX	RSP	RBP	RSI	RDI
	32bit	EAX	EBX	ECX	EDX	ESP	EBP	ESI	EDI	
	16bit 	AX	BX	CX	DX	SP	BP	SI	DI	
	8bit 	AH/AL	BH/BL	CH/CL	DH/DL
64bit , 32bit 레지스터 영역을 사용하기 전 모든 영역을 0으로 변경
	-shellcode는 shellcode가 실행되기 전의 값을 그대로 사용
레지스터 초기화 방법
	-sub 명령어 사용
		-sub 명령어를 이용해 자기 자신의 레지스터의 값을 뺌
		-sub 명령어는 shell code의 앞 부분에서 사용 시 잘 동작
		-연산 결과에 따라 OF,SF,ZF,AF,PF,CF flag 값 설정
			-이로 인해 코드가 예상과 다르게 동작 가능
	-xor 명령어 사용
		-배타적 논리합은 자신의 값과 연산 시 무조건 0이 됨
		-AF를 제외한 정의 되지 않으며 OF,CF의 값은 지워지고 SF ,ZF,PF 는 결과에 따라 결정
		-xor 명령어가 sub 명령어보다 flag 영향을 덜 주기에 더 효율적
	-sub eax,eax나 xor eax,eax 와 같은 방식으로 코드 작성
