---
{
"title":"블로그 디자인 및 제작",
"tags":["html", "javascript", "design", "studying","vue","nodejs"]
}
---

## 2022-08-08

블로그 제작을 제작하면서 코딩도 코딩이지만 계속 디자인에 대해서 많은 고민을 하는것 같다.
기존의 `pagination` 방식과 `infinite scroll(이하 무한 스크롤)` 방식에서 많은 고민을 하였지만, 현재 많은 앱들의 흐름을 보면
인스타그램, 페이스북등과 같이 무한 스크롤을 많이 사용하는 것을 알 수 있다.

그렇게 해서 무한 스크롤 방식으로 다시 고치게 되었다.

블로그 스크롤 형식을 무한 스크롤로 하다보니 고쳐야할 것들이 한둘이 아니였다.

그래서 기존에 있던 방식에서 바꾸었다.

기존 버튼에서 누르면 데이터를 불러오는 방식에서 스크롤 하면 로딩하는 방식으로 고치다보니
바꿔야 하는 함수가 너무 많았다.

특히 스크롤 하면 위치를 파악하는 함수가 필요했다.

기존에 `pagination`을 사용하는 방식에서 무한 스크롤를 사용하다보니 필요없는 함수가 많아졌다.
그래서 기존에 있던 함수를 모두 주석처리하고 다시 만들면서 필요한 것들은 주석을 푸는 형식으로 고쳤다.

이렇게 고치는 방식이 좋은 방식은 아니지만 블로그를 만들면서 너무 많은 계획과 실패를 반복하여 생긴 버릇인 것 같다.

```typescript
        /**
         * 스크롤 이벤트를 헨들링해주는 함수입니다.
         * 
         * @param event 스크롤 이벤트가 들어오는 매개변수입니다.
         */
        handleScroll(event:Event){
            this.post.yPos = (event.target as HTMLElement).scrollTop

            this.post.sections.forEach((section,index) => {
                const htmlElement = section as HTMLElement
                let top = (this.post.container as HTMLElement).scrollTop;
                let offset = htmlElement.offsetTop - 150;
                let height = htmlElement.offsetHeight;

                if (top >= offset && top < offset + height){
                    this.post.index = index
                }
            })

            let bottom = (this.post.container as HTMLElement).scrollHeight - (this.post.container as HTMLElement).clientHeight - (this.post.yPos as number)

            if(bottom == 0){
                if(this.page.loading === this.page.current){
                    
                    if(this.page.current < this.page.total - 1){
                        this.page.loading++
                        this.settingTechView()
                    }
                }
            }
        },
```

이 코드가 나오기 까지 여러 과정을 거쳤는데 우선 ts혹은 js에 HTMLElement에 대한 scrollbottom의 항목이 없다는 것을 처음 알았다.

그래서 `HTMLElement`의 `post.container(블로그 포스트를 저장하는 통)` 의 총 `scrollHeight`, `clientHeight`와

`HTMLElement`의 `scrollTop` 현재 스크롤 높이 상황을 이용하는 것이다.

즉 블로그 포스트가 많으면 포스트의 통이 늘어나는 것을 이용하는 것이다.

`scrollHeight` - `clientHeight` - `scrollTop`으로 하면 scrollBottom이 나온다.

이렇게 해서 스크롤 행동 감지를 통해 `scorllBottom`이 0에 도달하면 새로운 포스트를 불러오는 방식이다.

그리고 함수가 많이 사용하지 않는 것들이랑 스파게티 코드처럼 필요없는 함수들과 줄일 수 있는 함수들을 많이 없앨 수 있는 기회가 되었다.

그리고 현재 천천히 하고 있기는 하지만 그래도 절반 가까이 완성하였다. 이번 방학 전까지 tech 섹션에 있는 모든 것을 데스크톱, 태블렛, 안드로이드, ios까지
완료할 것이고 차근차근이 현재 완성한 프로젝트와 하고 있는 프로젝트를 놓을 수 있는 섹션까지 완성할 것이다.

이번에 대학교 성적이 나쁘지 않게 나와서 조기졸업이 가능할 것 같아서, 사실 이번 블로그 홈페이지를 업데이트하고 더 나갈 수 있을 지는 모르겠다.

하지만 블로그 디자인과 추가가 아니더라도 우선 졸업 작품을 4학년 1학기 전까지 완성하고 싶어서 2학년 2학기 겨울방학부터 만들어 나갈 생각이다.

자세한 내용은 기제하기 그래서 하진 않지만, 그래도 모르는 내용이 있으면 그 모르던 내용들을 기제하기 위해서 이 곳에 들어와서 기재할 것이다.
