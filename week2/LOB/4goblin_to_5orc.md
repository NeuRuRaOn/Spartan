#week2  
##lob  
###goblin_to_orc  
orc.c를 확인하면 argv[2]에 쉘 코드를 넣어야 한다는 감이 온다.( 사실 그 감이 없어서 이리저리 다 찔러 봤다)  
gdb로 분석해보면 main 함수에서 esp를 0x2c(44)만큼 빼는 것을 알 수 있다.  
따라서 버퍼의 시작주소는 main의 return 주소로부터 44byte라는 것을 알 수 있다.  
브레이크 포인트를 main+197(strcpy가 끝난 시점)에 걸어준다.  
 r $(python -c 'print("\xbf"*48+" "+"A"*125)') 명령어를 시행해준 후  
x/100s $ebp+100을 통해 argv[2]의 주소를 알아낸다.  
다음 payload를 NOP(40) +SFP(4)+RET(4)+' '+SHELLCODE(125)와 같이 구성하여  
다음 키를 얻어낸다.   
이 문제는 구글링을 통해 풀이를 참고하였다. 혼자 낑낑거리면서 풀려고 했는데  
도저히 풀리지 않는다.  
