---
{
"title":"백준 1005번 문제 해결 과정",
"tags":["c", "baekjoon", "algorithm", "studying"]
}
---


--//
..first link{+first+}
..second link{+second+}
..third link{+third+}
//--


여러 문제를 풀어보기도 하고 어려가지를 이미 해보았지만... solved.ac에서 처음으로 골드 문제를 한번 풀어 보았다. 좀 호기롭게 했는데, 결과가 너무 처참했다...

사실 1001번 부터 계속 풀어왔고 번호 순서대로 풀어보려고 하였다.(하지만 내가 한 이행동이 얼마나 정신나간 짓인지 깨닫게 되었다.)

처음 해결 과정을 어떻게 해서 시도 해봤는지에 대해서 먼저 설명하고 다른것들도 설명해보도록하겠다.

사실 나는 순서가 정해져있는지 알았다.

시작을 1로 해서 계속 숫자가 증가해 나가는 형식인 것으로 알고 있었다.

![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/baekjoon1005/image1.jpg?raw=true)

![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/baekjoon1005/image3.jpg?raw=true)
우선 간단하게 설명을 해보도록 노력을 하겠지만 잘 할 수 있을지는 미지수긴 하다.

앞서 위에서 이야기했듯 필자는 첫 정점 노드가 __무조건 1__ 인줄 알았다.

그래서 이렇게 생각하였다. 

처음에 주어지는 가장 건축 시간을 저장해두고 그 노드의 간선에 따라 주어진 건축 시간을 더해 나간 후에 입력 마지막 줄에 주어지는 노드 위치를 출력시 자동으로
출력이 되게 하는 방법이 좋을 것 같았다.

그리고 여러개의 간선들이 모이는 노드일 경우에는 가장 큰 값으로 저장해두어서 2개 이상의 건축물들이 다 만들어지는 시간이 되게 해주었다.

![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/baekjoon1005/image2.jpg?raw=true)

그래서 나온 코드가 이렇게 된 코드다.


```c
#include <stdio.h>
#include <stdlib.h>

typedef struct 
{
    int * totalTime;
    int count;
}curentTime;

void main(){
    int repeatCount;            //첫줄 -> 반복되는 총 횟수
    int buildCount,buildRule;   // 입력 두번째 줄 -> 건물 개수, 건물 건설 순서 개수
    curentTime times[1001];     // 현재 건축물의 총 건축시간
    int buildDelay[1001];       // 각각의 건축물의 건축시간
    int buildDuration;          //건축시간 입력 임시 변수
    int beforeBuild, afterBuild;//건축 순서 입력 임시 변수
    int startPoint;             //원하는 건축물의 총 시간

    int longest = 0,node; 
    scanf("%d",&repeatCount);   //총 횟수를 받아온다.
    for(int repeat = 0; repeat < repeatCount; repeat++){
        scanf("%d %d",&buildCount,&buildRule); //두번째 줄 입력
        for(int i = 0;i<buildCount;i++){
            scanf("%d",&buildDuration); //건축 시간 입력 buildCount번만큼 반복
            buildDelay[i] = buildDuration; //
            times[i].count = 0; //구조체의 totalTime의 길이 데이터이다
        }
        for(int i = 0;i<buildRule;i++){
            scanf("%d %d",&beforeBuild,&afterBuild);
            beforeBuild--; //index시작이 0인것을 감안하여 1을 뺴준다.
            afterBuild--;
            if(times[beforeBuild].count == 0){ //time첫 읽으시에 동적할당 첫 초기화
                //이전 빌딩에 대한 정보를 넣게 위에 사용함 그리고 재 선언 방지를 위해 count가 0일때만 실행
                times[beforeBuild].totalTime = (int *)malloc(sizeof(int)*(++times[beforeBuild].count));
                //이전 빌딩에 건축이 다만들어지는 시간(이하: 건축시간)을 저장
                times[beforeBuild].totalTime[times[beforeBuild].count - 1] = buildDelay[beforeBuild]; 
            }
            
            if(times[afterBuild].count == 0) //이후 빌딩에 대한 정보가 없을 경우(array count가 0일 경우)에 실행한다.
                //이후 빌딩의 건축시간을 저장
                times[afterBuild].totalTime = (int *)malloc(sizeof(int)*(++times[afterBuild].count)); 
            else
                //이미 동적할당이 배정이 되어있는 상태라면 재배정을 통한 크기 확충및 데이터 저장
                times[afterBuild].totalTime = (int *)realloc(times[afterBuild].totalTime,sizeof(int)*(++times[afterBuild].count)); 
            //여기서는 하나의 노드에서 2개 이상을 받을 수 있었기에 계속 추가 가능한 형태로 만들어 주었다.
            times[afterBuild].totalTime[times[afterBuild].count - 1] = buildDelay[afterBuild];
            //임시로 만드는 변수이다. 이변수는 노드가 2개이상인 경우에 가능 오래 걸리는 시간을 저장한다.
            int tempBiggest = 0; 
            for(int i = 0; i < times[beforeBuild].count;i++){
                if(tempBiggest < times[beforeBuild].totalTime[i])
                    tempBiggest = times[beforeBuild].totalTime[i];
            }
            //가장 오래걸리는 시간을 이후 빌딩 건축시간에 더하여 총 건축시간을 만든다.
            times[afterBuild].totalTime[times[afterBuild].count - 1] += tempBiggest;
        }
        //데이터가 원하는 위치의 인덱스 값을 받는다.
        scanf("%d",&startPoint);
        int biggestResult = 0;
        for(int i =0;i < times[startPoint - 1].count;i++){
            if(biggestResult < times[startPoint - 1].totalTime[i])
                biggestResult = times[startPoint - 1].totalTime[i];
        }
        //그 노드의 가장 큰 값을 출력한다.
        printf("%d\n",biggestResult);
    }
    return 0;
}
```

코드가 난잡하다 이쁘지 않다... 주석 처리도 안되어있어서 다시 읽어가면서 해석했다... 주석 다는 것에 대한 습관을 반드시 길러놓자...

이렇게 만들어서 잘 될 줄 알았지만 나온 결과물은..... 런타임 에러... 될 줄 알았지만 어림도 없다는 걸 알려주는 것인지 살짝 살짝 수정하였지만 4번이나 런타임 에러가 떠버렸다.

![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/baekjoon1005/image4.png?raw=true)
하하하;;;

그래서 결국 찾아봤다.

솔직히 마음이 양심에 찔렸다. 그래도 하루동안 고민하고 만들어봤다는것에 대해 위안을 삼고 찾아보니 **위상 정렬** 이라는 방식이 있다는 것을 깨달았다. 

그래서 블로그에있는 글들을 읽어봐도 솔직히 처음에는 이해는 했지만 코드로 어떻게 구상을 해야되는지 감이 잡히지 않았다.


```c
#include <stdio.h>
#include <stdlib.h>
typedef struct lineData{
    int count; //데이터를 가지고 있는 개수
    int totalTime;//지금 현재 총 건축시간
    int arr[1000];//간선들의 정보를 넣어줄 배열
}lineData;
void push(lineData * data,int next){
    (*data).count++;
    (*data).arr[(*data).count - 1] = next;
}

int main(){
    int i; //for구문의 변수
    int repeatCount;//총 반복 횟수
    int buildCount,buildRule;//짓는 건축 개수, 건축 순서의 정보 개수
    int before,after;//이전 건물, 이후 건물 변수
    scanf("%d",&repeatCount);//총 반복 횟수 입력
    int buildDelay[1001];//각각의 건물의 건축시간
    
    int outDegree[1001];//각 노드의 받는 간선의 개수
    lineData graph[1001];//각 노드의 간선 정보
    int queue[1000];//정점의 큐
    int count = 0;//큐 인덱스를 받기 위한 변수
    lineData result;//위상 정렬 출력 배열
    for(int repeat = 0;repeat < repeatCount; repeat++){
        scanf("%d %d",&buildCount,&buildRule);

        for(i =0;i<buildCount;i++){
            int temp; 
            scanf("%d",&temp);//건축시간 입력
            //각각의 건축물에다가도 넣고 각 노드의 간선 정보의 최종시간에 넣어준다.
            buildDelay[i] = graph[i].totalTime = temp;
            graph[i].count =0;//나중에 재사용될 변수임으로 초기화해줌
            outDegree[i] = 0;//위와 마찬가지임
        }
            
        for(i =0;i<buildRule;i++){
            scanf("%d %d",&before,&after);//이전 빌딩 이후 빌딩의 입력
            push(&graph[before - 1],after);//이전 빌딩의 인덱스에 이후 빌딩의 숫자를 넣어줌
            outDegree[after - 1] += 1;//간선개수의 정보에 하나를 추가함
        }

        while(1){
            count = 0; //0을 받은 횟수이자 큐의 인덱스
            //총 건물의 개수만큼 for구문 실행
            for(i =0; i < buildCount; i++){
                //정점인 인덱스를 찾아봄
                if(outDegree[i] == 0){
                    //찾은 경우에 큐에 추가함
                    queue[count] = i + 1;
                    count++;
                    //한번 정점이 되어 큐에 들어간 인덱스는 -1로 설정되어 정점 검사시에 확인되지 않도록 한다.
                    outDegree[i] = -1;
                    push(&result,i + 1);
                }
            }
            //정점이 없는 경우 while구문을 탈출한다.
            if(count == 0) break;

            for(i = 0;i <count;i++){
                for(int j = 0;j < graph[queue[i]- 1].count;j++){
                    int nodeNum = graph[queue[i] - 1].arr[j] - 1; //간선이 가야할 노드의 숫자
                    //간선의 총 시간이 간선의 기존 건축시간과 이전 건축물의 총 건축시간의 합보다 작을 경우
                    //간선이 가야할 노드의 총 시간을 바꿔준다.
                    if(buildDelay[nodeNum] + graph[queue[i] - 1].totalTime > graph[nodeNum].totalTime){
                        graph[nodeNum].totalTime = buildDelay[nodeNum] + graph[queue[i] - 1].totalTime;
                    }
                    //간선의 총 개수를 하나 줄인다.
                    outDegree[nodeNum] -= 1;
                }
            }
        }
        int resultIndex;
        //찾고자하는 인덱스의 값을 받는다.
        scanf("%d",&resultIndex);
        //인덱스 출력
        printf("%d\n",graph[resultIndex - 1].totalTime);
        
    }
    return 0;
}
```


