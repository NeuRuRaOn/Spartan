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
