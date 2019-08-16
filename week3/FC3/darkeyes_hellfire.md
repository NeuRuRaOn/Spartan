# week1  
## FC3  
### dark_eyes hell_fire  
ls을 통해 hell_fire.c를 확인하고 소스 코드를 읽었다.  
여기서의 hint는 another fake ebp or got overwriting이다  
이 문제는 256 byre의 char 형 배열 buffer와 4byte의 saved_sfp, 1024 byte의 temp가 선언되어 있다.  
그리고 fgets 함수를 통해 temp에 1024 byte를 입력받는다.  
strcpy 함수를 사용하긴 하지만 null terminate되는 함수이다.  
솔직히 c 코드만 한참 보고 gdb 좀 두드리다 또 writeup을 확인했다.  
여기서 주소 고정되는 영역은 stdin의 임시 버퍼 영역이라고 한다. 
break를 메인에 걸고 disas main을 하면 fgets()함수의 인자로 들어가는 stdin(0x8049788)의 주소를 볼 수 있다.  
main() 함수가 호출되기 전의 ebp를 사용해 fake ebp를 활용해야 한지만 main() 함수의 ebp를 사용하지 못하는 것을 해결한다.  
따라서 이 공격의 payload는 쉘코드(25)+nop(243)+leave_ret(4)+dummy(88)+fake ebp(4)+leave_ret(4)+mprotect(4)+&stdin(4)+1024(buffer size)(4)+7(buffer 권한값)(4)를 사용한다.
다시 풀어봐야할 듯 합니다.  
