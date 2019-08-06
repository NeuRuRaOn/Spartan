#week2
##lob
###gremlin_to_cobolt
gate 문제와 다른 점은 buffer의 크기가 16으로 제한되어 쉘 코드를 한번에 넣을 수 없는 것이다.  
따라서 SHELLCODE를 export해서 nop sled 기법을 사용해준다.  
여기서 nop sled는 말 그대로 nop 썰매로 nop가 10개 있다면 10줄을 그냥 skip 해주는 것이다.   
다음 getenv 함수를 통해 SHELLCODE 주소를 확인한다.   
getenv 함수는 name이 가리키는 문자열과 일치하는 문자열을 찾기 위해 환경 변수 리스트를 탐색하는 함수이다.    
다음 저장시켜둔 쉘코드를 python을 통해 집어넣어주면 key를 얻을 수 있다.   
중간에 cannot execute binary file 떠서 한참을 보고 있다가    
껐다켰더니 되는 건 무슨 심보일까   
