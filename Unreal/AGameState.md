# AGameState

### Inheritance Hierarchy
AActor -> AInfo -> AGameStateBase -> AGameState

---

AGameState는 게임의 상태를 정의하는 객체이다.\
AGameMode는 서버에만 있는 것과 달리 AGameState는 클라이언트에도 존재한다.\
Replicated 데이터를 클라이언트에 동기화하는 역할을 한다.

대표적인 데이터로 남은 시간, 라운드, 매치상태 등이 있다.

예시 데이터
```cpp
    UPROPERTY(Replicated)
    int32 TeamAScore;
```

참고: 데이터가 PlayerArray의 경우\
GameState는 기본적으로 TArray<APlayerState*> PlayerArray를 보유한다. 멀티플레이에서 같은 접속자들의 닉네임이나 스탯등 상태 정보를 공유하기 위함.

