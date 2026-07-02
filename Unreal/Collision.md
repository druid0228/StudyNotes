# Collision
Collision Channel / Collision Response

Collision은 3단계

```
1. Object Type
        ↓
2. Collision Channel
        ↓
3. Collision Response
```

1. Object Type\
객체의 신분\
Pawn, Vehicle, PhysicsBody, WorldDynamic, ...

2. Collision Channel\
누가 누구를 검사할 것인지\
Visibility, Camera, Weapon ...\
커스텀 채널 많이 사용함

3. Collision Response\
각 채널에 대해 어떻게 반응할지 딱 3개\
Block, Overlap, Ignore

### Collision Enabled
No Collision : 충돌하지 않음\
Query only : 조회만\
Physics Only : 물리 계산만\
Query and Physics : 둘 다\

Query Only는 Raycast, Overlap, Trigger 등에 사용한다.\
일반적인 캐릭터나 물리 객체는 Query and Physics를 많이 사용한다.


### Generate Overlap Events
이 옵션이 꺼져있으면 Response = Overlap 이어도\
Overlap Event가 발생하지 않는다.\


### Simulation Generates Hit Events
이 옵션이 꺼져있으면 Response = Block 이어도\
Hit Event가 발생하지 않는다.


### Line Trace
선을 쏴서 무엇이 맞는지 검사.

```cpp
FHitResult Hit;

GetWorld()->LineTraceSingleByChannel(
    Hit,
    Start,
    End,
    ECC_Visibility
);
```
4번째 인자가 채널에 관련


### Sweep

true이면 이동 경로를 따라 충돌을 검사한다.
false이면 충돌 검사 없이 목적지로 즉시 이동한다.

예시\
SetActorLocation(NewLocation, true) 에서
true 옵션이 sweep 이동하면서 충돌 검사도 실시\
SetActorLocation(NewLocation, false) 이 경우에는\
검사 없이 해당 위치로 그냥 이동

### FHitResult
충돌의 결과 정보

대표 멤버
```cpp
GetActor()
GetComponent()
Location()
ImpactNormal()
Distance()
.bBlockingHit
```
