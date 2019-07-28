#week1 
##bandit 
###bandit0to10 

0. bandit0to1 
  ls을 하여 readme 파일을 찾고 cat으로 읽는다. 
1. bandit1to2 
  cat ./-를 통해 key를 획득 
2. bandit2to3 
  cat spaces\ in\ this\ filename으로 획득가능 
3. bandit3to4 
  find inhere를 할 시 
  inhere./hidden 파일의 존재 확인 
  이 파일을 cat하여 key 확인 
4. bandit4to5 
  inhere directory로 이동 후 
  10개의 파일을 cat을 한번에 하면 key 획득 가능
5. bandit5to6
  find -size 1033c 방식으로 주어진 조건의 
  파일을 찾아 key를 얻음
6. bandit6to7
  cd ..을 두번 해준 후
  find - user bandit7 -group bandit6 -size 33c
  명령어를 통해 저장위치를 찾을 수 있음
7. bandit7to8
  grep "millionth" data.txt로 key를 찾을 수 있음
8. bandit8to9
  sort data.txt | unip -u로 찾을 수 있음
9. bandit9to10
  string "data.txt" | grep "=========="을 통해 key를 찾을 수 있음
  "=X10"은 string 함수를 사용해보면 확인 가능
10. bandit10to11
  base64 -di data.txt를 통해 
  값을 얻을 수 있음

