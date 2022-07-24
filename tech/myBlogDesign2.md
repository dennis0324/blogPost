---
{
"title":"블로그 디자인 및 제작",
"tags":["html", "javascriopt", "design", "studying","vue","nodejs"]
}
---

## 2022 - 07 - 21

만든다고 얘기하고 난 후에 약 5개월이 지났다. 공부 안한게 아니라 정확히 말하면 대학교 생활에 엄청 치이면서 살았다. 고등학교 생활보다 대학교 생활을 더 열심히 한거 같다.
평소에 코딩을 하면서 지내왔지만, 이번 대학교 생활 텀 프로젝트로 인해서 만들던 모든 프로젝트를 중지하며 대학 생활을 하였다. 

그래도 열심히 집중해서 그런지 다행이도, 좋은 성적으로 마무리하였다.

다시 본론으로 넘어와서 현재 블로그 현황에 대해서 말해보겠다.
현재 블로그의 전반적인 디자인을 다 수정하여 바뀐것이 엄청 많다. 

기존에 일반 html 방식에서 vue-cli 방식으로 다시 전면적으로 수정하여 제작하였다.
> 디자인 변경 후
> ![image](https://user-images.githubusercontent.com/50710829/180042749-97058fd2-09ad-4f63-bea0-b3a886754171.png)

현재 완성된 이미지이다.

전에 포스트와 현재의 포스트와 비교하면 알 수 있듯이 엄청 많이 바뀌었다는 것을 알 수 있다. 

> 디자인 변경 전
> ![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/finaldesign.png?raw=true)

사실 변경한 계기는 오랜만에 블로그를 제작하다 보니 만들었던 블로그가 너무나도 마음에 들지 않았다.
그래서 다시 전반적으로 수정하기로 마음을 먹고 고쳤다.

> ADOBE XD 전반적인 디자인 
> ![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign2/xdCap.png?raw=true)

이렇게 디자인을 마무리 짓고 vue에서 알다 싶히 컴포넌트를 제작후 서로 합치는 방식으로 제작하다니깐 
각 파트를 나누어서 제작하고 서로 붙이는 과정을 거쳤다.

![image](https://user-images.githubusercontent.com/50710829/180045028-e52f3225-7fd4-45c0-b03e-d36897331d24.png)

이렇게 로고를 담당하는 파트와

![image](https://user-images.githubusercontent.com/50710829/180045065-04fa1025-b350-480b-98bb-4cc5b00a9302.png)

네비게이션을 담당하는 파트로 나뉘어 제작하게 되었다.

심플한 디자인으로 홈 화면에 디자인하고 조금 특별한 방식으로 tech 페이지를 디자인해보았다.

> ![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign2/Tech_dark.png?raw=true)


위에거는 XD에서 디자인한 모습이고,
아래 모습은 실제 디자인한 모습이다.

![image](https://user-images.githubusercontent.com/50710829/180047001-f52c1a27-40dd-4326-b4a4-ed00c72ddc28.png)

완전히 디자인을 다 완성한건 아니지만, 추가적으로 모든 2달의 최대값이 62일 이므로 전체 표시값을 62로 정하고 2달의 실제 값을 더 진하게 표시하는 방식으로 제작하였다.

위에 블로그 활동 표시 인디게이터 같은 경우에는 `vue`의 `watch`사용하여 특정 `property`가 변경되면 값에 따라 인디케티터가 변경되는  방식이다.

```typescript
// BlogPostStream.vue

watch:{
    /**
     * BlogPostData가 들어오면 circleIndicator가 사용할 수 있도록 만들어주는 `watch`함수입니다.
     * 
     * @param newBlog 변경된 사항이 있으면 들어오는 매개변수입니다.
     */
    BlogPostData(newBlog){
        /** proxy{} 없애주는 작업입니다. */
        const PostChanged = JSON.parse(JSON.stringify(newBlog))

        const date = this.startingDate as Date;
        let tempDate = new Date(date.getFullYear(),date.getMonth(),date.getDate() + 1,date.getHours(),date.getMinutes(),date.getSeconds());
        const startingDate = new Date(tempDate.getFullYear(),tempDate.getMonth() - 2,tempDate.getDate() - 2,date.getHours(),date.getMinutes(),date.getSeconds())

        const blogPostStreamDatas:BlogPostStreamData[] = []
        while(startingDate.toISOString().split('T')[0] !== tempDate.toISOString().split('T')[0]){

            /** 저장하기 위한 임시 변수 생성 */
            const blogPostStreamData:BlogPostStreamData = {} as BlogPostStreamData

            /** 현재 루프 돌아가고 있는 date 앞 날짜만 가지고 오기 */
            const a = tempDate.toISOString().split('T')[0]

            blogPostStreamData.type = []

            /** 현재 가지고 있는 모든 포스트 확인해보기 */
            for(const node of PostChanged){
                if(a === node.createdat.split('T')[0]){
                    blogPostStreamData.type.push(0)
                }
                else if(a === node.updatedat.split('T')[0]){
                    blogPostStreamData.type.push(1)
                }
            }
            blogPostStreamData.date = a;
            tempDate?.setDate(tempDate?.getDate() - 1);
            blogPostStreamDatas.push(blogPostStreamData)
        }
        this.blogPostStreamData = blogPostStreamDatas
    }
},
```

우측과 마찬가지로 좌측도 비슷한 방식으로 만드는 것이다. 


```typescript
// GithubStream.vue

watch:{
    async startingDate(){
        console.log('staringDate',this.startingDate)
        /** 외부 백엔드 서버에서 특정 데이터를 받아옵니다. */
        const response = await axios.get(`http://localhost:3000/githubContirbutions/?startingDate=${this.startingDate}`)
        const githubResponse = response.data.data as githubContributionResponse 
        if(response !== undefined){
            console.log(githubResponse)

            const githubMonths = githubResponse.user.contributionsCollection.contributionCalendar.months.slice().reverse()
            const githubWeeks = githubResponse.user.contributionsCollection.contributionCalendar.weeks.slice().reverse()

            this.githubDataMonth = githubMonths;
            this.githubDataWeek = githubWeeks;
        }
    }
},
```

사실 이 부분을 설명하면 백엔드 부분의 코드도 같이 설명해야한다.


```typescript
app.get('/githubContirbutions/',async (req,res) => {
    const responseQuery = req.query.startingDate as string
    const startDate = new Date(responseQuery)
    const QUERY = `
                query testing($userName:String!, $toDate:DateTime, $fromDate: DateTime) { 
                    user(login: $userName) {
                        contributionsCollection(from: $fromDate, to: $toDate) {
                            contributionCalendar {
                                totalContributions
                                weeks {
                                    contributionDays {
                                    weekday
                                    date 
                                    contributionCount 
                                    color
                                    contributionLevel
                                    }
                                }
                                months  {
                                    name
                                    year
                                    firstDay
                                    totalWeeks  
                                }
                            }
                        }
                    }
                }`;
    const queryValue = {
        "userName":"dennis0324",
        "toDate":new Date(startDate.getFullYear(),startDate.getMonth(),startDate.getDate(),startDate.getHours(),startDate.getMinutes(),startDate.getSeconds()),
        "fromDate":new Date(startDate.getFullYear(),startDate.getMonth() - 2,startDate.getDate() + 1,startDate.getHours(),startDate.getMinutes(),startDate.getSeconds())
    }
    const endpoint = "https://api.github.com/graphql"
    let commitDatas = await fetch(endpoint,
    {
        method: 'POST',
        headers: {
        'Content-Type': 'application/json',
        'Authorization':`bearer ${GITHUB_TOKEN}`
        },
        body:JSON.stringify({
        query:QUERY,
        variables: queryValue
        })
    })
    commitDatas = await commitDatas.json()
    res.send(commitDatas)
})
```

위에 코드는 express를 사용한 벡엔드 부분이다. 이렇게 해서 깃허브에서 기여 활동을 받아올 수 있다. 
