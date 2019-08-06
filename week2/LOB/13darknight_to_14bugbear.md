#week2  
##lob  
###darkknight_to_bugbear  
bugbear.c의 주석부분에 rtl1이라고 적혀있는데 여기서 rtl이란  
return-to-libc의 약자로 바이너리에 원하는 함수가 없어도 메모리에  
적재되어 있는 공유 라이브러리 함수를 이용하여 공격하는 기법이라고 한다.   
cp를 하여 gdb를 열어준 후 브레이크포인트를 main 주소에 걸어준다.  
r을 하고 p system을 하여 libc system의 주소를 얻는다.  
이제 간단한 c 코드를 통해 /bin/sh의 문자열의 주소를 확인한다.(여부분 구글 참조/구글은 사랑)   
페이로드는 Dummy(40)+SFP(4)+&system()(4)+Dummy(4)+&"/bin/sh"(4)로   
아래와 같은 코드로 bash를 확인한다.  
 ./bugbear $(python -c 'print("A"*44+"\xe0\x8a\x05\x40"+"AAAA"+"\xf9\xbf\x0f\x40")')  
진짜 풀면서 이건 복습해야 겠다는 생각이 든다.  
