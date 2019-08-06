#week2  
##lob  
###golem_to_darkknight  
darkknight.c를 열어보고 순간 짧길래 쉬울까하고 기대했다.  
하지만 이 문제를 알아보려고 검색하다보니 이 문제는 sfp overflow라는 기법을 사용해야 한다고 한다.  
sfp overflow는 공부해서 따로 올린 생각이지만 간단하게 얘기하면 단 한 바이트만으로  
instruction pointer의 흐름을 제어하는 공격 기법이라고 한다.  
우선 darkknight를 sarkknight로 cp한 후 gdb를 열어준다.  
그리고 이전 문제들과는 다르게 main함수의 프롤로그 즉 ebp esp의 설정이 끝난 직후인  
main+3에 브레이크 포인트를 걸어준다.   
다음 "A"를 41개를 넣고 실행시킨다.   
그 다음 problem_child의 프롤로그와 에필로그가 각각 끝난 직후에 브레이크 포인트를 걸어  
ebp와 esp를 info reg를 통해 확인한다.  
이러한 과정을 통해 main 함수가 에필로그가 진행될 때도  
return address가 아닌 0xbffffa41+4로 저장되는 것을 확인 가능했다.  
이제 shellcode(25)+nop(15)+&buf 주소-4를 통해 bash를 확인가능하다.  
화아 sfp overflow 너무 어렵다  
