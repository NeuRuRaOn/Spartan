# 동적할당
## C언어 메모리 구조
C언어의 메모리 구조는 세가지로 이루어져 있다. 데이터 영역, 스택 영역, 힙 영역
### 데이터 영역
java의 메서드 영역과 비슷한 영역으로 전역 변수와 static 변수가 할당되는 영역이다. c는 java와 달리 class 구조 안에 모든 것을 포함시키지
않기에 클래스 정보가 올라가지 않는다. 데이터 영역의 정보는 프로그램의 시작과 동시에 올라가고,
프로그램이 끝나야만 소멸이 된다.
### 스택 영역
java의 호출스택과 동일한 역할을 한다. 함수가 호출되면 함수내의 지역 변수와 매개 변수가 저장되는 영역이다. 함수 호출이
끝나면 소멸된다. 스택이라는 이름과 같이 활동한다. 예를 들어 
~~~c
int main(){
int a=1;
lsm1();
lsm2();
}
void lsm1(){
int b=1;
}
void lsm2(){
int c=1;
}
~~~
이렇게 할 경우 스택에 main이 들어가게 되고 내려오면서 lsm1도 스택에 들어가게 된다. int b가 스택에 할당된다. lsm1이
끝나가고 나면 b는 스택에서 삭제된다. 다음 lsm2와 c도 같은 순서를 반복하게 된다. stack의 push와 pop 개념을
생각하면 편하다.
### 힙 영역
할당해야 하는 메모리의 크기 등이 프로그램이 실행되는 동안 즉 런타임에 결정될 때 사용되는 영역이다.
~~~c
int main(){
int i=10;
int arr[i];
}
~~~
예를 들어 위의 배열의 크기는 main이 실행되어 i가 스택 영역에 할당되어야 결정된다. 이와 같은 상황에서 사용되는 것이
힙영역이다.
## malloc
malloc이란 c 함수에서 동적으로 할당하는 함수이다. 동적으로 할당하는 이유는 c언어의 메모리 구조 때문이다.
### 함수원형
~~~c
#include <stdlib.h>// 이 헤더파일을 include 해야 사용 가능
void *malloc(size_t size)
~~~
### 할당해제
메모리 릭이 발생하지 않으려면 할당 해제는 꼭 해주어야 하는데 이때 사용해주는 명령어가 free이다.
### 사용예시
~~~c
#include <stdlib.h>
int main(){
int num;
scanf("%d",&num);
pArr=(int *)malloc(sizeof(int) *num);
for (int i=0;i<num;++i)
  scanf("%d",&pArr[i]);
free(pArr);
return 0;
}
~~~
예시는 위와 같다. num 즉 pArr의 크기가 run time 중에 결정된다. 따라서 pArr을 동적할당 시켜주는 것이다.
메모리 개수만큼 동적 할당되어야 하기에 sizeof(int)*num을 해주는 것이다. 그리고 사용을 완료하고는 free를 꼭
해주어야 한다.
## calloc
calloc 함수는 형태는 조금 다르나 malloc 함수와 비슷한 역할을 한다. 
~~~c
#include <stdlib.h>
int main(){
int num;
scanf("%d",&num);
pArr=(int *)calloc(5,sizeof(int);//곱하기를 따로 적을 필요 없이 앞에 수를 적어준다
for (int i=0;i<num;++i)
  scanf("%d",&pArr[i]);
free(pArr);
return 0;
}
~~~
### malloc과 calloc의 차이점
둘의 차이점은 malloc은 할당된 공간의 값을 건드리지 않는 반면, calloc은 할당된 공간의 값을 모두 0으로 바꾼다.
따라서 0으로 초기화가 필요할 시 많이 사용되게 된다.
## realloc
realloc은 이미 malloc이나 calloc으로 할당된 공간의 크기를 바꿀 때 사용해준다.
~~~c
#include <stdlib.h>
int main(){
int num;
scanf("%d",&num);
pArr=(int *)malloc(sizeof(int) *num);
for (int i=0;i<num;++i)
  scanf("%d",&pArr[i]);
realloc(pArr,sizeof(int)*5);
free(pArr);
return 0;
}
~~~
여기선 num값을 받아서 pArr의 사이즈를 설정시킨 후 다시 5로 크기를 바꾸어준다. 4*num 바이트를 할당했다가
20바이트를 다시 할당시킨다. 








