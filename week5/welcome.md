# PWNABLE.XYZ 
## welcome 
~~~c
__int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  _QWORD *v3; // rbx
  __int64 v4; // rdx
  char *v5; // rbp
  __int64 v6; // rdx
  size_t v7; // rdx
  size_t size; // [rsp+0h] [rbp-28h]
  unsigned __int64 v10; // [rsp+8h] [rbp-20h]

  v10 = __readfsqword(0x28u);
  sub_B4E(a1, a2, a3);
  puts("Welcome.");
  v3 = malloc(0x40000uLL);
  *v3 = 1LL;
  _printf_chk(1LL, "Leak: %p\n", v3);
  _printf_chk(1LL, "Length of your message: ", v4);
  size = 0LL;
  _isoc99_scanf("%lu", &size);
  v5 = (char *)malloc(size);
  _printf_chk(1LL, "Enter your message: ", v6);
  read(0, v5, size);
  v7 = size;
  v5[size - 1] = 0;
  write(1, v5, v7);
  if ( !*v3 )
    system("cat /flag");
  return 0LL;
}
~~~
ida를 통해 main을 확인해보면 다음과 같은 코드를 확인 가능하다.  
~~~
Welcome.
Leak: 0x7f7153538010
Length of your message: 140124706013201
Enter your message: 
cat: /flag: No such file or directory
~~~
위와 같이 동작하는 프로그램이다.
length of message:에서 받은 값을 size라는 변수에 저장시키고
size는 다시 v7에 저장하고 v5[size -1]은 0으로 초기화시켜준다.
malloc이라는 함수가 눈에 띄었는데 사이즈를 전달하는 곳에 전부 malloc()함수를 전달하고 있었다.
그래서 malloc을 고장내보기 위해 10000000000000000 정도를 size에 집어넣었다.
고장이 나서는 enter your message: 다음으로는 넘어가지 않는다. 
그러나 flag는 내뱉지 않음으로 size를 잘 조절해봐야겠다. 
왠지 size -1을 해주는 것이 신경쓰여서 leak로 본값을 hex->dec해준후 1을 더해 size에 집어넣었다.
cat /flag라는 문구가 보인다. 근데 그런 file이 없다고 한다. 알고보니 local로 계속 시도중이었다.ㅋㅋㅋㅋㅋ
선배한테 물어본 후 nc로 연결했더니 flag 확인
