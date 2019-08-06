#week2
##lob
###cobolt_to_goblin
cat goblin.c를 통해 여긴 gets 방식을 사용하는 것을 알 수 있다.  
gets 방식에 bof를 일으키는 방법은 cat과 | 를 사용한다는 것을  
구글이 알려주었다. 그래서 우선 cobolt와 마찬가지로 SHELLCODE를   
export 한 후 그 주소를 getenv()함수를 통해 알아내었다.  
그 다음 (python -c 'print("\x90"*20+"\x83\xfe\xff\xbf")'; cat) | ./goblin  
와 같은 코드로 답을 얻어내었다.  
앞 문제에서 고생해서 그런지 이 문제는 요상하게 잘 풀린 느낌이다.  
