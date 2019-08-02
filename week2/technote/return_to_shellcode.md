#week2  
##technote  
###return_to_shellcode  
1. return to shellcode  
       * return address 영역에 shellcode가 저장된 주소로 변경해, shellcode를 호출하는 방식  
2. call & ret instruction  
    * call 명령어: call 명령어 다음 명령어의 주소 값을 stack에 저장 후 피연산자 주소로 이동  
    * ret 명령어: pop 명령어를 이용해 rsp 레지스터가 가리키는 stack 영역에 저장된 값을 rip에 저장 후, 해당 주소로 이동
    * stack 영역의 저장된 return address 값 변경 -> 프로그램 흐름 변경  
3. proof of concept(call & ret instruction)  
       # call의 경우 함수의 호출 전과 호출 후를 비교하면(call 명령어에 의해 stack에 호출된 참수 종료 후 이동할 주소 값을 저장)   
       stack: 0xffffffe4b0 -> 0x7fffffffe4a8  
       value: 0x4004f0 -> 0x4004eb  
       # ret 명령어의 경우 stack에 저장된 값인 주소 값을 rip 레지스터에 저장해 헤당주소로 이동( 따라서 stack에 저장된 return address 변경 가능 -> 공격자가 원하는 영역으로 이동가능)  
4. permission in memory
       프로그램에서 사용되는 3가지 권한  
              * read:메모리 영역의 값 읽기 가능  
              * write:메모리 영역의 값 적기 가능
              * execute:메모리 영역의 코드 실행가능  
        GCC(GNU에서 보호하는 파일)는 기본적인 DEP(데이터 실행 방지)가 적용되기에 코드가 저장된 영역만 실행권한 가지고 저장된 영역은 실행권한이 없음  
        shellcode 실행을 위해선 execute(실행) 권한이 설정되어 있어야 함  
        
