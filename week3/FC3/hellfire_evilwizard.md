# week3  
## FC3  
### hell_fire evil_wizard  
코드에서의 힌트를 보면 GOT overwriting을 볼 수 있다.  
앞에서 사용되었던 ret sled 기법은 length라는 변수를 두어 방어되었다.  
따라서 여기선 rop 기법이 사용되어야 한다.  
이 문제를 풀기 위해 필요한 것은 
  * printf()의 PLT GOT
  * strcpy() PLT  
  * system 함수 주소  
  * "/bin/sh" 문자열 주소  
  * system() 함수의 실제 주소에 해당하는 각 문자의 주소  
objdump -d 명령어를 통해 printf_plt와 got 그리고 strcpy_plt 주소를 알아낸다.  
gdb에 main에 break걸고 r 해서 system 주소를 얻어낸다.(p system)  
objdump를 통해 pop_ret_ret를 구성하는 문자의 주소들을 따로 알아낸다.  
이제 strcpy_plt+ppr+priintf_got+system_addr+printf_plt+dump(4)+"bin/sh"주소로 페이로드를 구성하여 bash를 확인하다.  
하아 죽여줘 차라리  
