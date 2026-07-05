# Component Lifecycle

Component도 자체 생명주기를 가진다.\
Actor가 생성될 때 Component도 함께 초기화 되며,\
활성/비활성에 따라 별도의 함수들이 호출된다.

## 기본흐름

```
Constructor
↓
InitializeComponent*
↓
BeginPlay
↓
Tick
↓
EndPlay

* bWantsInitializeComponent == true 인 경우
```

<details> <summary>일반적인 호출 순서</summary>

```
Constructor
↓
OnRegister
↓
InitializeComponent
↓
BeginPlay
↓
TickComponent
↓
EndPlay
↓
UninitializeComponent
↓
OnUnregister
↓
BeginDestroy
```
</details>

### Constructor

객체 생성 시 가장 먼저 호출

기본값 설정, Tick 설정, Subobject 설정

GetWorld 사용은 적절치 않다

자주 보는 코드
```cpp
UMyComponent::UMyComponent()
{
    PrimaryComponentTick.bCanEverTick = true;
}
```

### OnRegister

```cpp
virtual void OnRegister() override;
```
Component가 Actor에 등록(Register)될 때 호출

주로 엔진 내부(Render, Physics, Scene 등록 등)에서 사용된다.

주의: OnRegister는 한 번만 호출 되는 함수가 아니므로,\
게임 초기화 용도로 사용하면 안된다.

### InitializeComponent
```cpp
virtual void InitializeComponent() override;
```
Component를 초기화하기 위해 호출되는 함수.\
다른 Component 참조, 초기 데이터 준비.\
bWantsInitializeComponent가 true인 경우 호출된다.\
이 시점에는 다른 Component들도 생성 및 등록되어 있어 참조가 가능하다.

### BeginPlay
```cpp
virtual void BeginPlay() override;
```
게임 시작 시 호출된다.\
Timer, Delegate 등록, Gameplay 초기화

### TickComponent
```cpp
virtual void TickComponent(
    float DeltaTime,
    enum ELevelTick TickType,
    FActorComponentTickFunction* ThisTickFunction) override;
```
매 프레임 호출된다.\
상태 갱신, 이동 계산, 지속적인 검사 등에 사용\
필요 없다면 꺼두는 것이 좋다.


### EndPlay
```cpp
virtual void EndPlay(
    const EEndPlayReason::Type EndPlayReason) override;
```
게임 종료 또는 Actor 제거 시 호출된다.\
Timer, Delegate, 리소스 등을 해제

### UninitializeComponent
```cpp
virtual void UninitializeComponent() override;
```
Component 초기화가 해제될 때 호출되는 함수.\
플러그인이나 엔진에서 사용하는 경우가 많다.

### OnUnregister
```cpp
virtual void OnUnregister() override;
```
Component가 등록 해제될 때 호출된다.

Scene, Physics 등 엔진 시스템에서 분리되는 시점이다.


### BeginDestroy
```cpp
virtual void BeginDestroy() override;
```
GC가 객체를 제거하기 직전에 호출된다.\
게임코드에서 잘 사용되지 않고 UObject 내부 리소스 정리에 사용