# week3  
## FC3  
### evil_wizard dark_stone  
문제 푸는 것이 앞의 문제와 비슷한 형태라 심적으로 조금 더 편했다.  
절대 쉬웠다는 것은 아니다.  
여기서도 앞에서와 같이 payload에 필요한 것들을 먼저 나열하면  
  * strcpy() 함수의 plt
  * printf() 함수 plt,got
  * system() 함수 실제 주소  
  * "/bin/sh" 문자열 주소
  * system 함수의 실제 주소에 해당하는 각 문자의 주소
앞의 방법과 똑같이 알아낸 후 payload를 작성하면 된다.  
문제 풀면서 어디선가 반전 있겠지 긴장하면서 풀었는데 그냥 풀렸다.  
롸업 보니까 방법 원래 똑같은 거라 해서 뭔가 기분 좋았다.  
