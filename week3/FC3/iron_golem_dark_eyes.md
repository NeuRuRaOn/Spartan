# week3  
## FC3  
### IRONGOLEM TO DARKEYES  
ls해보면 "어두운 눈까리들"을 확인가능하다.  
cat dark_eyes.c를 해보면 hint가 ret sleding인 것을 알 수 있다.  
정말 힌트가 없으면 내가 이 문제를 한 시간은 잡고 있을지 모르겠다.  
확인해보면 그 이유는 saved_sfp가 선언되어 있기 때문이다.  
다른 분의 writeup을 참고하자면 buffer로 argv[1]이 복사된 후  
buffer+264(buffer 크기에 dummy 크기 8 byte가 더해진 위치) 즉 main()함수의 sfp 위치에  
saved_sfp의 값을 다시 복사한다.  
솔직히 저 write up 없었으면 어쩔까 싶다.  
이 문제는 ret sled와 execl()함수를 사용해야 한다.  
우선 gdb를 켜고 main() 함수의 주소 중 최하위 byte가 0x00을 갖는 값을 가리키는 주소를 찾는다.  
또한 stack aslr에 의해 stack 주소가 아니어야 한다.  
main()함수의 ebp+24의 주소에 shell spawning 프로그램을 작성해야 한다.  
전 문제와 같게 gdb에서 벗어나 p execl 명령어를 통해 execl() 함수의 주소를 찾아준다.  
objdump -d dark_eyes | grep ret 명령어를 통해 ret 명령어의 주소를 얻는다.  
전 문제에서 사용된 shell spawning 프로그램을 작성 후 ebp+24 즉 0x83ed3c에 컴파일한다.  
다음 dummy(268)+&ret *3(12)+&execl()(4) payload를 통해 bash를 확인한다.  
흐아아아아아앙
