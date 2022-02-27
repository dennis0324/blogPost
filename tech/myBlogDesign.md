
---
{
title: "블로그 디자인 및 제작",
tags: [html, javascript, design, studying]
}
---

# 블로그 디자인 기록
   
--//
..first link{+first+}
..second link{+second+}
..third link{+third+}
//--

## 2022-02-14
{+first+}
처음 시작했을 때는 별 생각 없이 시작했던 것 같다. 

나름 디자인에 자신있다고 생각했던 것 같은데 막상 해보니깐 그렇지도 않은 것 같다는 생각이 들었다. 2022년 2월 초에 시작하게 된 블로그 만들기는
2월 21일이 된 와중에도 다 디자인을 완료하지 못했다.

그래도 나름 현재까지 진행 상황과 어디까지 만들었는지에 대해서 천천히 읊고 기록해 놓고자 작성하게 되었다.


![](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/design1.png?raw=true)
별 생각없이 만들긴 했지만 그래도 이때 만들 당시만해도 괜찮다는 생각이 들었다.

남들과는 좀 다른 홈페이지를 만들어 보려고 하였지만 결과는 처참한거 같다.

나름 다른 디자인들도 만들어 보겠다고 만들던 것들에서 약간식 수정하여 여러 디자인들을 만들어 보았지만,
디자인에 견문이 넓던 것도 아니였기에 아쉬운 결과가 나온 것 같다.

>첫번 째 디자인
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/design2.png?raw=true)   

>두번 째 디자인
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/design3.png?raw=true)   

>세번 째 디자인
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/design4.png?raw=true)   

>네번 째 디자인
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/design5.png?raw=true)   

![alt text](https://jekyllthemes.io/images/themes/simples-jekyll-theme.webp?_cchid=eccad4a7c25f449a0df27824207f324b)
>[original website](https://jekyllthemes.io/theme/simples-blog-jekyll-theme)

위에 이미지는 내가 구글링 하다가 발견한 사진으로 필자가 봤을 때는 깔끔해보이는 사이트 디자인이라고 생각하였다.
그래서 이것저것 찾아보니 사이트가 더 다채워지는 요소 중 하나가 사진의 유무라는 것을 디자인을 찾아보다가 느끼게 되었다.

내가 만든 게임이 있었다면 사진과 같이 블로그를 만들었겠지만 만든 게임이 없어서 아쉬울 따름이다. 

사진이 없기에 만들면서 그냥 심플하게 만들기로 결심했다. 

어짜피 코딩과 공부 기록 목적이라면 알록달록 이쁜 것도 좋겠지만, 많은 색깔이 들어가는 걸 안 좋아하는 나로써는
단순한 색과 단순한 디자인이 더 마음에 끌렸기에 이렇게 디자인한것 같다.
 
 >최종 디자인
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/finaldesign.png?raw=true)

이렇게 해서 대문 디자인을 대충 끝난 상태였지만, 아직 프로젝트 페이지에 들어갈 rest api 와 디자인을 만들지 못하였다. 물론 tech 페이지도 말이다...

## 2022-02-23

대문이 얼추 완성 되고 시작되는 tech 파트에 대한 기록이다.

디자인을 하기 전에 그래도 어려운거 먼저 끝내겠다는 마인드를 가지고 rest api를 이용해서 github를 통해 블로그 포스팅을 가져 와야겠다고 생각했다. 
물론 처음부터는 아니고 같은 대학교 동기 형이 있는데 동기형이 알려줬다. [jdeokkim]
(너무 감사하게 생각하고 있다. 형 아니였으면 NAS로 데이터베이스까지 건드려서 할 뻔했다. 사실 하려고 했다...)

그래서 예전에 이 블로그 제작 전에 너무 장황하게 만들어 놔서 아무것도 못하고 그냥 방치해둔 react로 만들던 프로젝트가 있는데 거기서 사용한 [octokit]를 사용하고자 한다. 

```js
//fileName: post.js

const octokit = new Octokit({auth: `token my PRIVARYCODE`})
```
로 시작해서 rest api를 사용하여 github에서 데이터를 불러올 생각이다.

```js
export const getPost = async (menuName) => {
   const path = await octokit.request("GET /repos/{owner}/{repo}/contents/{menu}",{
         owner:"dennis0324",
         repo:"blogPost",
         menu:menuName
       })
      ...
```
위에 코드는 나의 깃허브 페이지에서 blogPost라는 repository에서 데려오는 것이다. 
이를 통해서 불러 들인 포스트한 게시글들을 처음 테크 목차에 들어가면 무엇에 들어갈 것인지 결정해주는 역할이라고 생각하면 될것 같다.

```js
   ...
const testing = await octokit.repos.getContent({
      owner:"dennis0324",
      repo:"blogPost",
      path: "tech/myBlogDesign.md"
    })
    ...
```

이 코드는 올린 글 목록을 보는 것이 아닌 올린 글의 내용들을 다시 불러오는 것이다.
여기서 불러온 코드는 아래의 코드로 디코딩을 한 번 거쳐서 출력된다.

```js
    let decoded = Base64.decode(testing.data.content);
```

필자는 이 구간에서 생각보다 많이 헤메였다. 처음에 md 파일이 무슨 형식으로 인코딩 되어있는지도 자세히 몰랐고, 심지어 한글이 utf-8 형식이라 unicode에 맞지 않아 한글이 깨져서 나오는 것이다.
그래서 여기서 힘좀 많이 들였다.


찾아보니 md 파일은 *BASE 64* 로 인코딩 되어있었고 디코딩을 하기 위해서 다른 스크립트 파일을 이용해서 하면 된다는 것을 깨달았다. (필자가 이렇게 짧게 쓰긴 했지만 실력이 부족했어서 나름 오랬동안 찾아봤던 것 같다.)

```html
<script src="https://cdn.jsdelivr.net/npm/js-base64@3.6.1/base64.min.js"></script> <!--base64 해독 역할-->
```

이렇게 하니까 정상적으로 출력하는 것을 볼 수 있었다.
![alt text](https://github.com/dennis0324/blogPost/blob/main/data/pictures/myBlogDesign/restapiresult.png?raw=true)

이렇게 만들고 보니 글에서의 목차가 보이지 않다는 것을 깨달아버렸다.
그래서 고심하다가 나름 나만의 정의 만들어서 목차를 웹사이트 쪽에서 자동으로 생성하게 만들기로 하였다.

필자가 올린 위에 사진에 첫 라인에 보면 
```
--//
..first link{+first+}
..second link{+second+}
..third link{+third+}
//--
```
라는 부분이 있을텐데 이 부분이 목차를 생성할 때 도와주는 부분이라고 생각하면 된다.


```js
    postedIndexs.forEach((val,index) => {
      if(val.innerHTML.indexOf("--//") >= 0 && val.innerHTML.indexOf("//--") >= 0){
        var re = /[\n\-\/]/g; //엔터키, --// 또는 //--를 제거한다고 가장 앞에있는 ..를 제거한다.
        temp = val.innerHTML.replace(re,"").replace("..","")
        temp = temp.split("..") //..를 분해하여 배열로 설정한다.

        val.remove()
        

        //각 키와 연결 키를 설정해둔다.
        temp.forEach((key,index) => {
          //{+[linkName]+}를 분해하여 만든다.
          temp[index] = key.replace(/[\{]\s*/g,",").replace(/[\}\+]\s*/g,"").split(",")
        })
        console.log(temp)
      }
      else{
        temp.forEach((linkList,index) => {
          if(val.innerHTML.indexOf("{+"+linkList[1]+"+}") >= 0){
            console.log(val)
            const node = document.createElement("div") //div 설정
            node.classList.add(linkList[1]) //div 생성 및 클래스 생성하여 연결할 링크 설정
            node.innerHTML = linkList[1] //생성된 div에다가 텍스트 설정후 표시
            postMenu.appendChild(node) //생성된 div post-menu라는 아이디에다가 추가함
            val.classList.add("link",linkList[1]) //
            val.innerHTML = val.innerHTML.replace("{+"+linkList[1]+"+}","")
            
          }
          console.log()
  
        })
      }

    })
```

사실 실력이 좋지 않아 코드가 이쁘지 않다. 굉장히 난잡하다. 
그래서 블로그 다 만든 후에 코드 이쁘게 짜는 법좀 공부해 보려고 한다. 항상 만들다 보면 스파게티 코드가 완성되어 나 스스로 스트레스를 너무 많이 받는 것 같다.

사실 위 코드가 하는 행동은 간단하다. rest api에서 받아온 markdown코드를 누가 해석해주냐면 우리 [marked]가 해준다. 
이 marked가 각 문장마다 h1, p 등등으로 바꾸어서 만들어 주는데 목차를 거의 위에다가 놓아서 사실 목차 찾는 시간이 오래 걸리지 않아 그냥 foreach문으로 돌려버렸다.
더 좋은 방법이 있다면 찾아봐야겠다...

우선 모던 p를 불러와 '--//' '//--'가 동시에 존재 하는 지 확인하고 확인이 되면 엔터키, -, /를 제거 후 배열을 만들기 쉽게 가장 앞에 있는 ..만 없애준다.
그러면 
```
first link{+first+}..second link{+second+}..third link{+third+}
```
이런식으로 나올텐데 여기서 split으로 돌려서 단순히 배열을 제작한 것이다. 사실 더 간단한 방법이 있었지만 나중에 블로그 자체가 아닌 깃허브에서 봐도 헷갈리지 않게 만들기 위해 
나름대로의 보기 편안한 방법을 찾아서 한 것 같다.

그리고 {이름}를 분해하여 각각 짤라서 배열을 나눠 다시 아까 사용한 배열을 넣어 이중배열을 만들어 줬다.
그리고 나머지 else부분은 그냥 div 생성하고 안에 텍스트 및 이중배열에서 생성한 클래스 이름을 사용하여 노드 제작 후 index쪽에다가 넣어주는 것이다. 
그리고 이동하고 싶은 부분에 삽입하면 그 쪽에 있는 p라인에다가도 클래스를 넣는 방식이라고 생각하면 된다.

이렇게 해서 tech 목차에 사용할 rest api 부분은 대충 다 만들었다.


사실 디자인이 먼저라고 생각 하는 분들도 있을 것이라고 생각했지만 솔직히 api쪽 먼저 하고 받아오는 형식을 먼저 정하고 나중에 디자인을 정하는 것이 낫다는 생각이 들어
먼저 한 것이다. 디자인들도 여러가지 생각해봤지만 아직 딱 정해진 것 없는 것 같다. 항상 디자인으로 스트레스 받는 것 같다.

## 2022 - 02 -25
막힌 부분이 있어서 하루를 보냈다.

진짜 농담이 아니고 새벽 3시까지 모르는거 있어서 찾아보는 것도 대학교 과제하면서 이후로 오랜만인 것 같다. 

물론 군대를 갔다오면서 군대에서도 코딩을 시간 날 때 마다 했지만, 그래도 이렇게까지 오랫동안 붙잡고 있던 것도 오랜만이다.

사실 더 쉬운 방법이 있다는 것을 알고 있었지만 내 오기가 생겨서 그만 두지 못했다. 컴퓨터를 끄는 것은 새벽 1시에 껐지만, 너무 열받아서 새벽 세시까지 핸드폰으로 뒤져봤다.
그냥 너무 단순한 문제였는데 너무 힘들게 생각한게 아닌가 싶기도 하다.

중간에 포기하고 그냥 홈페이지 DB를 하나 만들어서 거기다가 저장 해둘까 생각도 했다만... 더 뭔가를 벌리면 완성도 못한다는 생각에 계속 찾아보았던 거 같다.

3시쯤 되어서야 뭐 내가 찾는거랑 비슷한거를 찾은거 같아서 북마크해두고 잤다.

사실 글 작성일 및 업데이트된 날짜를 가져오는 것인데, 파일별로 정리해서 모아둔게 없던 것이다. rest api로는 무리가 있겠다 싶어서 graphql를 사용해서 하려 하니깐
사실 아무것도 모르는 입장에서는 곤혹일 수 밖에 없었다.

찾아도 내가 원하는 게 아무것도 안 나와서 답답해 죽는 줄 알았다.

아침에 일어나서 결론 지은것은 미리 짜둔 기초 틀에 aliases를 사용하여 가져오는 파일들을 정리해서 가져오자고 계획를 하고 짜기 시작했다.

필자가 작성한 방법이 정론법은 아니지만, 내가 원하는 결과를 도출시켜주는 하나의 과정이기에 일단 올려서 기록에 남긴다.

```js
 const getData = await octokit.request("GET /repos/{owner}/{repo}/contents/{menu}",{
   owner:"dennis0324",
   repo:"blogPost",
   menu:menuName
 })
```

우선 restapi를 사용하여 파일이름을 다 한꺼번에 가져온다.

그리고 그 배열을 바탕으로 qeury 구문을 제작해준다.

```js
var commits = ``

 pathData.forEach((value,key) => {
   console.log(value,key)
   let putData = value[0].replace(".md","") + `: history(path: "`+value[1]+`", first: 100) {
     edges {
       node { 
         committedDate 
         oid
         author
         {  
           email
         }
       } 
     }
   }
   `
   commits = commits.concat(putData)
 })
```
우선 aliases를 사용할 수 있도록 String으로 이어붙여 내부 query를 만들어 준 후

```js
    const query = `query RepoFiles($own:String!,$repo:String!){
      repository(owner: $own, name: $repo) {
        first: object(expression: "main") {
          ... on Commit {
            `+commits+`
          
        }
      }
    }
  }
  `

```
로 완성한다.

그 후


```js
    const queryValue = {"own":"dennis0324","repo":"blogPost"}

    const endpoint = "https://api.github.com/graphql"

    try{
      fetch(endpoint,
        {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
            'Authorization':'bearer TOKEN'
          },
          body:JSON.stringify({
            query:query,
            variables: queryValue
          })
        })
        .then(res=> res.json())
        .then(json => console.log(json))
    }
    catch(e){
      console.log(e)
    }
```

fetch로 graphql를 불러온다. 이렇게 해서 포스트된 글, 포스트된 시간에 사용할 데이터 불러오는 것에 대한 코딩은 다 끝났다.


[octokit]: https://octokit.github.io/rest.js/v18
[marked]: https://github.com/markedjs/marked
[jdeokkim]: https://github.com/jdeokkim
