# week3  
## lazenca  
### SROP  
  SROP는 sigreturn 시스템 call을 통해 레지스터에 원하는 값 저장 가능  
  이 기법을 사용해 원하는 시스템 함수 호출
### Signal  
  * signal은 프로세스에 이벤트가 발생했음을 알림  
  * signal은 다른 프로세스에게 signal 전송 가능  
    * signal은 원시적 형태의 IPC(interporcess communication)으로 사용가능  
    * signal은 자기 자신에게도 signal 전송 가능 
  * signal은 일반적으로 커널이 송신하며, 다음과 같은 이벤트 종류가 있음  
    * 하드웨어 예외 발생한 경우  
    * 사용자가 시그널을 발생시키는 터미널 특수 문자 중 하나를 입력한 경우
      * Interrupt character(Control + c)  
      * Suspend character(Control + z)  
    * 소프트웨어 이벤트가 발생한 경우
      * 파일 디스크립터에 입력 발생  
      * 타이머 만료  
      * 해당 프로세스의 자식 프로세스 종료  
  * signal은 생성되면 프로세스에 전달되고 전달된 시그널의 종류에 따라 아래와 같이 동작 실행  
    * 시그널 무시  
    * 프로세스 종료  
    * 코어 덤프 파일 생성 후 프로세스 종료  
    * 프로세스 중지  
    * 프로세스의 실행을 재개  
### Signal handler  
  * signal handler는 프로그램이 특정 시그널의 기본 동작을 수행하는 대신 프로그래머가 원하는 동작을 수행하도록 변경 가능 
  * signal handler는 user mode 프로세스에 정의되어 있고 user mode 코드 세그먼트에 포함  
  * signal handler가 user mode에서 실행되는 동안 kernel mode에서 handle_signal()함수가 실행됨  
    * user mode에서 kernel mode로 진입시 user mode에서 사용중이던 context를 kernel stack에 저장  
    * kernel mode에서 user node로 진입시 kernel stack이 모두 초기화되는 것을 해결하기 위해 setup_frame(),sigreturn() 함수를 사용  
      * setup_frame() : user mode의 stack을 설정  
      * sigreturn() : kernel mode stack에 hardware context를 복사하고 user mode stack의 원래의 content?(시그널 문맥)를 저장  
  * signal handler 처리 순서  
    1. 인터럽트 또는 예외 발생 시 프로세스는 kernel mode로 전환  
    2. 커널은 user mode로 돌아가기 전에 do_signal() 함수 실행  
      * do_signal() 함수는 handle_signal()을 호출하여 signal을 처리  
      * handle_signal() 함수는 setup_frame()을 호출하여 user mode stack에 context를 저장  
    3. 프로세스가 user mode로 다시 전환되면 signal handler가 실행됨  
    4. signal handler 종료 시 setup_frame() 함수에 의해 user mode stack에 저장된 리턴 코드가 실행됨  
      * 해당 코드에 의해 sigreturn() 시스템 함수가 호출됨  
        * sigreturn() 시스템 함수에 의해 kernel mode stack에서 일반 프로그램의 hardware context를 user mode의 stack에 복사  
        * sigreturn () 함수는 restore_sigcontext()을 호출하여 user mode 스텍을 원래 상태로 복원  
    5. 시스템 호출 종료 시 일반 프로그램은 실행 재개 가능  
