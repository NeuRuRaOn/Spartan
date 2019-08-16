# week3  
## FC4  
### dark_stone cruel  
fake ebp에 적응한 거 같으니까 fake ebp 쓰지 말란다. 이거 만든 분 뭔가 공부할 수 있도록 해주셔서 고마운데 솔직히게 좀 싫다.  
이 문제는 풀기 위해 ramdom library에 ret sleding을 해야 한다.  
return address offset을 알기 위해 gdb에서 strcpy() 다음 바로 브레이크를 걸어줘서 buf에서 ebp까지의 거리가 0x100임을 확인한다.  
ret 명령이 실행 후 stack에 있는 주소 중 값이 고정되고 null이 가장 짧은 문자열을 가진 주소를 찾아야 한다.  
x/wx 명령어를 통해 0x008caff4가 0x008cad3c를 가리키는 것을 알아냈다.  
이제 shell spawning 프로그램 생성 후 ret sled를 0x008cff4-8로 이동시켜 execl()함수를 덮어주면 된다.  
하아 솔직히 이해가 딱 60퍼만 되서... 다시 보겠습니다.  
우선 다시 따라가보면 위에서 말한대로 setreuid와 setregid로 권한상승을 시켜주는(fc의 drop privilege 때문) shell spawning code를 짜주고  
"/bin/sh"로 실행시켜 줍니다.  
이제 objdump로 찾은 ret 주소를 사용하여 payload를 작성해준다.  
무작정 따라해서 솔직히 이해가 안 되어서 요약도 잘 안 되네요  
다시 돌아와서 고치겠습니다.  
