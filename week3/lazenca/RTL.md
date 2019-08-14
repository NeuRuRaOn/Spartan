# week3  
## lazenca  
### RTL  
RTL(Return to Libc)- Return address 영역에 공유 라이브러리 함수의 주소로 변경해, 함수를 호출하는 방식, 이 방식을
통해 NX bit(DEP)를 우회가능  
#### calling convention  
cdecl  
  * 인텔 x86 기반 시스템의 c/c++에서 사용되는 경우 많음  
  * linux kernel에서도 사용
  * 함수의 인자 값을 stack에 저장, 오른쪽에서 왼쪽 순서로 stack에 저장  
  * 함수의 return 값을 eax 레지스터에 저장  
  * 사용된 stack을 해당 함수를 호출한 함수가 정리함   

나머지 이것이 진행되는 과정은 옮기는 것을 시도하다 잘 되지 않아서 문제들을 다 풀고 코드들을 더 잘 이해한 후
다시 시도하도록 하겠다.
