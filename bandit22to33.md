#week1 
#bandit 
#bandit22to33 

22. bandit22to23  
  문제가 시키는 대로 /etc/cron.d로 이동하여 
  cat cronjob_bandit23을 보면 /usr/bin/cronjob_23.sh주소를 확인가능 
  이 파일을 cat하면 SHELL SCRIPT를 확인가능 
  myname 변수는 whoami를 사용시 bandit23이라는 것을 알 수 있다 
  my target은 echo I am user bandit23 | md5sum | cut -d ' ' -f 1을 실행시 
  구할 수 있다. 
  이제 echo에서 본대로 /tmp/$mytarget형태로 값을 넣어주면 key를 구할 수 있다. 
23. bandit23to24  
23번은 문제에도 나타나있듯이 상당히 까다로운 문제이다  
우선 tmp 디렉토리에 mkdir로 solving23이라는 디렉토리를 만들었다  
그리고 그 안에 solving23이라는 shell script를 짰는데  
#!/bin/bash  
cat /etc/bandit_pass/bandit24 > /tmp/solving23/passwd  
이런 형태이다.  
바로 chmod -R 777 . 명령어로 권한을 부여시켜주었다  
1분이 지나고 /tmp/solving23/passwd 파일을 확인하니  
key값이 들어있다.  
#궁금점-SH을 짜두고 CHMOD -R 777 . 부분을 참고하였는데  
정확히 이해되지 않는다  
24. bandit24to25  
이 문제는 쉘 코드를 짜는 것보다 timeout을 해결하는 것이 어려웠다  
tmp에 디렉토리를 만든 후 echo 함수를 통해 현재의 key에 0부터 9999까지를 붙혀서  
list.txt에 저장시키는 쉘 코드를 만들었다  
cat list.txt | nc localhost 30002 명령어를 통해  
key를 얻어냈다.   
필자는 timeout이 코드의 문제점인 줄 알고 3시간 동안 잡고 있었다  
25. bandit25to26  
간단하고 재밌는 문제이다  
more 함수 실행될 수 있도록 ssh -i bandit26.sshkey bandit26@localhost 명령어를  
실행하고 나서 화면의 크기를 줄여놓는다.  
그러면 more을 통해 v명령어를 통해 vim에 들어갈 수 있게 되고  
key값을 얻을 수 있게 된다  
이후 :set shell=/bin/bash를 한후  
:shell을 통해 bandit26로 이동  
26. bandit26to27  
bandit25에서 바로 bandit26로 연결 후  
tmp에 디렉토리를 만들고   
cat etc/bandit_pass/bandit27을 실행하는 sh을 만듬  
bandit27-do를 통해 sh을 실행시켜 key를 얻음  
27. bandit27to28  
디렉토리를 만들어준 후  
git clone ssh://bandit27-git@localhost/home/bandit27-git/repo을 하면  
clone이 생성됨  
cat을 통해 쉽게 key 획득  
28. bandit28to29  
27 to 28과 같은 방식으로 clone을 생성  
clone된 디렉토리에서 git log로 missing data 된 부분이 있는 것을 확인  
git checkout으로 missing data전의 branch로 이동  
cat README.md를 통해 key 확인  
29. bandit29to30  
앞의 두 문제와 같이 clone을 생성 후   
README.md파일을 확인하면 no password in production을 확인가능  
production의 의미는 제작소이고 구글링으로 헤매다 보니  
git branch -r을 통해 원격저장소를 볼 수 있는 것을 알게됨  
git branch -r에서 dev branch를 확인하고  
checkout을 사용하여 branch를 dev로 바꿔준다  
다시 cat README.md를 통해 key를 획득  
30. bandit30to31  
앞과 같은 방식으로 clone을 생성  
cat README.md나 git log로 정보를 얻지 못함  
헤매다 ls -al을 통해 .git 디렉토리 발견  
뒤지다 packed-refs 파일을 열어 refs/tags/secret을 확인  
여기서 tag는 특정 커밋을 가리키는 것을 의미  
여기서부터는 구글링을 통해 알게 됨  
git show secret을 통해 커밋 확인하여 key 얻음  
31. bandit31to32  
clone을 생성한 후 README.md를 확인하면  
file을 push하라는 것을 알 수 있음  
key.txt 파일을 cat을 통해 만들어줌  
git add key.txt를 방해하는 .gitignore를 rm을 통해 제거한 다음  
다시 git add key.txt를 해줌  
이후 git push를 통해 key를 획득  
매우 귀찮고 어려운 문제  
32. bandit32to33
앞의 문제가 어려워서 그런지 쉽게 느껴진 문제  
명령어가 동작하게 하기 위해 $0 명령어로 bash로 변환  
이후 cat /etc/bandit_pass에서 key 획득  
33. bandit33to34  
끝이드아아아아아아아아아아아아앗!!!!!!!!!!!!!!!!!!!!!!!!!!!!  




