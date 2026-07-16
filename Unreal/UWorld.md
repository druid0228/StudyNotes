# UWorld

### Inheritance Hierarchy
UObject -> UWorld

---

`UWorld`는 현재 게임 세계를 나타내는 객체이다.

Actor들은 모두 하나의 UWorld 안에 존재\
게임 플레이에 필요한 대부분 시스템도 UWorld가 관리하며\
가장 중요한 클래스 중 하나.


## 대표적인 기능들

### SpawnActor
```cpp
GetWorld()->SpawnActor<AMyActor>();
```
새로운 Actor를 생성한다.

### Timer
```cpp
GetWorld()->GetTimerManager();
```
TimerManager를 가져온다.

### Delta Time
```cpp
GetWorld()->GetDeltaSeconds();
```
현재 프레임의 Delta Time을 가져온다.

### PlayerController
```cpp
GetWorld()->GetFirstPlayerController();
```
PlayerController를 가져온다.

### Line Trace
```cpp
GetWorld()->LineTraceSingleByChannel();
```
Trace를 수행

World는 여러개가 존재할 수 있다\
예:\
* Editor World
* PIE
* Preview World
* Game World


## GameInstance와 관계
```
Engine
    │
GameInstance
    │
UWorld
    │
Actor
```
GameInstance가 World보다 오래 살아남는다.

## Constructor에서 GetWorld()를 쓰면 안 되는 이유

아직 World에 속하지 않았기 때문이다.

---

UWorld가 관리하는 것들
- Actor
- Level
- Physics
- Navigation
- Timer
- AI
- Line Trace
- Spawn