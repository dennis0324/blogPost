---
{
"title":"게임 제작해보자 1",
"tags":["python", "c", "raylib", "DQN", "c99", "C99", "shmem", "gamedev", "game-dev", "shared-memory"]
}
---

## 게임을 제작해보자

대학교 학기 중에 SW 동아리를 하게 되어 무언가를 제작해야하는 상황에 놓였었다. 짧은 시간동안 만들 수 있는게 녹록하지 않아 만들기 간단한 것으로 고민하던 중에 T-REX Dino라는 게임이 떠 올랐다.
10월 14일 부터 11월 25일까지의 아주 짧은 기간이라 무언가를 만들려면 최대한 빠르게 만들어야 했다. 그래서 T-REX Dino 게임 적합하다고 생각해서 바로 만들기 시작했다. 만드는 것도 중요하긴 하지만 이것저것 보면서 게임을 제작할 때 계획하는 것이 중요하다는 것을 알고 있었기에 간단하게 계획을 하고 만들기 시작했다. 프로그램을 제작하기 전에 간단하게 짠 것을 모식화면 다음과 같이 만들 수 있다.

- 장애물 관리자
  - 장애물 관리자 생성
  - 장애물 관리자 삭제
  - 장애물 관리자 초기화(게임 재시작 시)
- 장애물
  - 생성
- 캐릭터 
  - 출력
  - 움직임
- 텍스쳐 관리자
  - 출력

제작할 언어는 c언어와 [raylib](https://www.raylib.com/)를 사용해서 제작할 예정이다. 사실 동아리 활동이 이번 2학기만 이뤄진 것이 아닌 지난 1학기도 동아리 활동을 하였다. 하지만 2학년인 제일 바쁜 시간으로 과제와 기간이 겹쳐 하지 못하였기 때문에 1학기의 연장선으로 1학기때 사용하던 [raylib](https://www.raylib.com/)를 사용하여 제작하고자 하였다. 

본격적으로 구현하는 파트로 넘어와서 가장 먼저 제작할 것은 `텍스쳐 관리자`와 `장애물 관리자`, `장애물`가 아닌 캐릭터 움직을 재현하는게 우선이라고 생각했다. 움직임을 구현하기 가장 쉬운 방식은 캐릭터 앞으로 직접 움직이는 것이 아닌, 뒤의 배경과 장애물이 움직이면서 `플레이어`가 움직이는 것 "처럼" 보이게 하는 방식을 사용했다. `플레이어`를 만들면서 편리하게 수정하고 관리할 수 있도록 객체처럼 사용하는 것을 원했기에 구조체에 함수 포인터를 넣어서 사용하였다. 구조체 안에 가장 먼저 `aabb`[^1] 와 화면의 사이즈 저장하는 변수와 기본 플레이어의 위치를 저장하는 `idle_pos`를 만들어 주었다. 

객체처럼 사용한다고 하였으니 표시 함수에 대한 함수 포인터 지정을 해야한다. 그렇기에 초반에 구조체를 설계할 때 다음과 같이 설계하였다.(현재는 구조체가 많이 바뀌었다.)
```c
// player.h
typedef struct Player{
    Rectangle aabb;
    Vector2 idle_pos;
    Vector2 velocity;
    bool isJump;
    void (*move)(struct Player * p);
    void (*show)(struct Player * p);
}Player;
```
이렇게 선언 한 후에 

```c
void DrawPlayer(Player *player) {
    player_move(player);
    ApplyGravity(player);
    DrawRectangleRec(player->aabb, RED);
}
```
`플레이어`의 출력과 관련된 함수를 제작한 후 플레이어 생성에 관련된 함수안에서 포인터 함수와 연결해주었다.

```c
Player * create(float x_size, float y_size){
    ...
    p->show = DrawPlayer;
    ...
}
```

`플레이어`의 움직임은 점프만 구현하면 되기 때문에 매우 간단하다. 움직임도 학교에서 간단히 배운 가속도와 속도 거리에 관련된 공식만 알면 간단하게 구현할 수 있는 내용이다.

```c
void ApplyGravity(Player *player) {
    if (player->aabb.y < player->idle_pos.y) //점프 상태
        player->velocity.y += GRAVITY; //중력 가속도 넣어준다.

    const double deltaTime = GetFrameTime();

    player->aabb.y += player->velocity.y * deltaTime; //넓이 값 구한다.
}
```
이렇게 간단하게 구현이 되지만 추후에 이 함수 때문에 엄청 고생한다...

[^1]: 막 지은것 같지만 실제로 `Axis Aligned Bounding Box`라고 불리는 녀석이다.
