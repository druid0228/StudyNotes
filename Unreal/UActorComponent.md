# UActorComponent

### Inheritance Hierarchy
UObject -> UActorComponent
---

UActorComponent는 Actor에 기능을 추가하기 위한 가장 기본적인 Component이다.

World에 직접 존재하지 않는다, Actor에 붙어서 동작한다.

Owner를 가진다. 항상 자신을 소유한 Actor를 가진다.

Transform이 없다. 위치가 필요하면 USceneComponent를 사용한다.

`GetWorld` UActorComponent도 사용 가능하다.\
내부적으로는 Owner의 GetWorld()를 호출한다.


대부분 Actor의 생성자에서 만든다.
```cpp
HealthComponent =
    CreateDefaultSubobject<UHealthComponent>(
        TEXT("HealthComponent"));

// 런타임에는
auto* HealthComponent = NewObject<UHealthComponent>();
```

Tick을 갖는다.

Activate / Deactivate로 활성화 비활성화가 가능하다.

주로 사용하는 예
* 체력
* 인벤토리
* 버프
* 저장
* 퀘스트
등등 논리를 분리할 때.


### CreateDefaultSubobject() vs NewObject()

|CreateDefaultSubobject | NewObject|
|-----------------------|----------|
|Constructor에서 사용    |런타임 사용|
|Default Component 생성  | 동적 생성|
|자동 등록 과정 포함       |필요시`RegisterComponent()` 필요|


### RegisterComponent()
런타임에
```cpp
auto* Comp = NewObject<UMyComponent>(Actor);
Comp->RegisterComponent();
Actor->AddInstanceComponent(Comp); // 경우에 따라
```
NewObject로 생성한 Component는 RegisterComponent()를 호출해야 월드에 등록되어 정상적으로 동작한다.



### Activate()
Deactivate는 활성 상태를 변경하는 것이다.\
Tick여부는 PrimaryComponentTick 설정과 연관\
Active가 항상 Tick을 멈추는 것은 아니다.
