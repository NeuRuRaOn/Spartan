# HACKCTF 
## Basic_BOF#1  
lob에서 연습했던 부분을 쉽게 사용할 수 있는 문제였다.
주어진 파일을 먼저 ida를 통해 확인했고 v5를 -559038737로 만들어주어야겠다고 생각했다.
그다음 리눅스의 gdb를 통해 disas main를 해보았고
0xdeadbeef와 cmp를 해주는 것을 확인가능했다.
따라서 oxdeadbeef를 40개의 dummy와 함께 nc 해주어 flag를 얻을 수 있었다.
문제를 푼 뒤 특이한 writeup을 찾을 수 있었는데 이는 gdb-peda를 이용해야 한다.  
우선 pattern_create라는 명령어를 통해 문자열을 생성한다.
그후 run을 해주고 pattern_create에서 생성된 문자열의 뒤에 4자리수를 순서를 리틀인디안에 맞게 순서를 바꿔준다.  
ex) aa0a >a0aa
다음 pattern_offset 찾을 문자(ex a0aa)이나 이것의 약자인 patto a0aa라는 명령어를 통해 문자가 저장된 offset을 얻을 수 있다.
여기서 offset이 40이라는 것을 얻었고 금방 더미의 크기를 정할 수 있었다.
앞으로 유용하게 사용할 거 같아서 정리해둔다.
## Basic_BOF#2  
우선 시키는 대로 nc를 해보면 하아아아아앙을 확인할 수 있다.
당황스럽다. 문제 내는 것도 쉬운 일은 아닌 듯 싶다.
disas main을 통해 shell 함수의 주소를 확인 가능하다.
이제 exploit을 구성하기 위해 더미를 얼마나 넣을지 찾아본다.
a를 120개 넣고 런을 해보면 eax 값인 0x80484b4가 129번째에 등장하는 것 확인
따라서 128개의 더미 값과 앞에서 얻은 shell 명령어의 주소 값을 넣어준다.
## Basic_fsb  
우선 fsb를 조사했다. format string bug의 약자로 사용자의 입력에 의해서 프로그램의 흐름을 변경시킬수있는 취약점이다.
개념을 계속 읽어봤는데 아리까리해서 이 문제를 writeup을 참고해서 풀면서 이해해봤다.
사실 따라해봤지만 fsb는 어렵다. 이해는 아 몰랑
이 문제는 이후에 fsb를 이해하고 풀었다는 평이 많아서 저도 그렇게 하도록 하겠습니다.ㅠㅠ
## 내 버퍼가 흘러넘친다!!!  
이문제의 코드는 ida32bit로 확인가능 64에서 순간 안 되길래 당황
이 문제는 read함수로 0x32바이트만큼 받은 후 gets 함수로 버퍼를 읽는다.
name 변수의 주소는 0804A060으로 확인
버퍼 s의 크기는 0x14이므로 s(0x14) | sfp(0x04) |ret(0x04) 로 페이로드를 구성
python exploit 코드를 짤때 쓴 함수들
우선 pwntool을 사용해야 한다.
remote: 리눅스의 nc와 동일하다고 생각하면 된다.
recvuntil:말 그대로 어떤 문자열이 나올 때까지 정보를 받는다고 생각하면 된다.
sendline: 문자열을 remote로 연결시킨 포트에 보내준다.
따라서 이 문제에서는 name에 sendline으로 미리 shellcode를 넣어주고
다음 input 함수에 name의 주소를 집어넣은 payload를 집어넣어 버퍼를 흘러넘치게 하는 방식으로 풀어낸다.
이제 짜놓은 파이썬 코드를 사용하면 shell에 도달가능하다.
## X64_Buffer_overflow  
~~~python
from pwn import *
r= remote("ctf.j0n9hyun.xyz",3004)

payload = 'A' *0x118 +p64(0x400606)
r.sendline(payload)

r.interactive()
r.close()
~~~
우선 nc ctf.j0n8hyun.xyz 3004를 했더니 받은 정보에 hello를 앞에 붙여 돌려준다.
아이다를 통해 코드를 확인한 결과 s는 0x10c 바이트이고 v5는 int이므로 4바이트이다.
또한 callmemaybe 함수에서 shell을 얻을 수 있는 것으로 확인
따라서 s(0x10c) | v5(0x04) |sfp(0x08) | ret의 페이로드를 파이썬으로 짜서 실행시켜
shell 권한을 얻는다.
문제를 풀다보면 lob보다 비교적 간단한 거 같다.
