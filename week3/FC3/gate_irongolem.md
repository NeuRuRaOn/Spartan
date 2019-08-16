# week3  
## FC3  
### GATE -> IRON_GOLEM  
이 문제 풀면서 lazenca 몇번을 다시 읽었는지 모르겠다.  
우선 고맙게도 iron_golem.c를 보면 fake ebp라는 힌트를 얻을 수 있다.  
따라서 execl 함수를 사용하여 sfp에 execl 매개변수 주소를 넣고 ret에서 execl 주소를 넣어둔다.  
이 문제의 경우 vim /etc/passwd를 통해 bash2로 바꿔줄 필요 없는 듯하다.  
맞습니다. 저는 뻘짓거리한 거....  
우선 gdb 켜주고 disas main 해준다.  
main +3에서 \x108만큼 스택을 할당해주는 것을 통해 
  buffer[256]  
  dummy[8]  
  sfp[4]  
  ret[4]
와 같은 형태를 띄는 것을 알 수 있다.  
readelf -S 명령어를 통해 got 주소를 알아낸다.  
또한 gdb에서 p execl 명령어를 execl 함수의 주소를 알아낸다.  
여기서 롸이트업을 보고 알게 된 것은 페이로드에는 ebp의 값을 바꿔주는 함수의 프롤로그를 생략한 execl 주소에 3을 더해준 값으로 
페이로드를 구성해야 한다는 점이었다.  
따라서 페이로드는 dummy[264] +got의 주소+execl의 주소의 형태를 가진다.  
하아 너무 어렵다.  
