#week1 
#bandit 
#bandit11to21 
11. bandit11to12 
  이것은 rot 13 문제임 *rot13-굉장히 간단한 암호화방식, 알파벳을 13자리 로테이션
  cat data.txt | tr a-zA-Z n-za-mN-ZA-M으로 key 확인가능 
12. bandit12to13 
  귀찮은 문제 
  xxd -r을 통해 hexadump 값을 추출 
  file을 통해 gzip되어 있음을 확인 
  mv로 파일 이름을 -.gz로 바꾼후 압축 풀기 
  file로 계속 압축되어 있는 형태를 확인하며 
  풀다보면 key를 얻을 수 있음 
  어렵다기보단 더러운 문제 
  뒤에 문제를 푼 입장에서 그래도 이 문제에 왕창 
  압축시켜 놓고 나중에는 압축 문제는 없음 
13. bandit13to14 
  쉬운 문제이지만 나중에 나오는 거의 모든 문제들의 기초 
  ssh -i 파일이름 bandit14@localhost 방식으로 
  다음 단계로 넘어감 
  /etc/bandit_pass/bandit14에서 key 확인가능 
14. bandit14to15 
  nc localhost 30000을 통해 포트와 연결후 
  현재 키 값을 보내면 됨 
15. bandit15to16 
  openssl s_client -connect localhost:30001 을 통해 
  연결이 가능함 
  현재 키를 보내면 키 획득 가능 
16. bandit16to17 
  nmap localhost -p 31000-32000 시도시 
  두 개의 포트를 확인 
  openssl을 시도시 한 곳만 응답을 하고 
  rsa 키를 보냄 
  이것을 tmp에 파일을 만들어 저장 후 
  chmod 600 를 통해 권한 상승시킴 
  ssh -i 을 사용하면 문제 해결 
17. bandit17to18 
  diff passwords.new passwords.old를 통해 key 획득 
18. bandit18to19 
  이 문제는 강제로 닫히는 쉘을 유지하기 위해 
  ssh -t 옵션을 사용 
  t 옵션 사용시 강제적으로 /bin/sh을 사용가능 
  위의 방법으로 bandit18에 접속 
  cat readme 
19. bandit19to20 
  setuid를 이해하기 위한 간단한 문제 
  ./bandit20-do cat /etc/bandit_pass/bandit20 명령어를 통해 
  bandit20 key 획득 
20. bandit20to21 
  이 문제의 경우 두개의 터미널을 열어야 한다 
  하나는 nc -l -p 31321 명령어를 통해 31321포트에 연결시키고 
  다른 하나는 ./suconnect 31321 명령어를 통해 31321포트에 연결시킨다 
  그 후 nc를 연 터미널을 통해 현재 key값을 보내면 답이 돌아온다. 
21. bandit21to22 
  cat /usr/bin/cronjob_bandit22.sh을 한 후 
  거기서 얻은 주소로 이동 후 key 획득 
  
