# PWNABLE.XYZ
## misalignment
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char s; // [rsp+10h] [rbp-A0h]
  _QWORD v5[3]; // [rsp+18h] [rbp-98h]
  __int64 v6; // [rsp+30h] [rbp-80h]
  __int64 v7; // [rsp+38h] [rbp-78h]
  __int64 v8; // [rsp+40h] [rbp-70h]
  unsigned __int64 v9; // [rsp+A8h] [rbp-8h]

  v9 = __readfsqword(0x28u);
  setup(*(_QWORD *)&argc, argv, envp);
  memset(&s, 0, 0x98uLL);
  *(_QWORD *)((char *)v5 + 7) = 3735928559LL;
  while ( (unsigned int)_isoc99_scanf("%ld %ld %ld", &v6, &v7, &v8) == 3 && v8 <= 9 && v8 >= -7 )
  {
    v5[v8 + 6] = v6 + v7;
    printf("Result: %ld\n", v5[v8 + 6]);
  }
  if ( *(_QWORD *)((char *)v5 + 7) == 47244640437LL )
    win();
  return 0;
}
~~~
IDA를 통해 위와 같은 코드를 확인할 수 있다. 마지막 if 문을 만족시킬 경우 win()을 통해 flag 획득이 가능하다. v6,v7,v8을 사용하는 형태가 앞의 add 문제와 비슷하다.
문제를 풀면서 v5[0]를 

