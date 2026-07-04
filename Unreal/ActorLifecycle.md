# Actor Lifecycle

`SpawnActor` 또는 레벨 로드로 Actor가 생성될 때 호출되는 주요 흐름.

## 생성 / 초기화 흐름

1. Constructor\
생성자가 가장 먼저 호출
```cpp
AMyActor::AMyActor()
{

}
```
Component 생성 및 기본값 설정
```cpp
CreateDefaultSubobject<USceneComponent>(TEXT("Root"));
```

주의: 아직 World에 등록되기 전이므로 GetWorld()에 의존하는 작업은 적절하지 않다.

---
2. PostInitProperties
```cpp
virtual void PostInitProperties() override;
```
UObject의 Property 초기화가 끝난 직후 호출된다.\
주로 엔진, 플러그인, 에디터 확장 쪽에서 사용하며 일반 게임 코드에서는 자주 사용하지 않는다.

---
3. PostLoad / PostActorCreated

생성 경로에 따라 둘 중 하나가 호출된다.
```cpp
virtual void PostLoad() override;
```
`PostLoad`는 Asset이나 Level에서 로드된 Actor에 호출된다.\
저장 데이터 복원, 버전 호환 처리 등에 사용된다.

```cpp
virtual void PostActorCreated() override;
```
`PostActorCreated`는 `SpawnActor`로 새로 생성된 Actor에 호출된다.

PostLoad와 PostActorCreated는 배타적이다.\
즉, 로드된 Actor는 PostLoad, 새로 Spawn된 Actor는 PostActorCreated 쪽을 탄다.

---
4. OnConstruction
```cpp
virtual void OnConstruction(
    const FTransform&
);
```
Construction Script의 C++ 버전\
Actor 배치, 위치 변경, Details 변경, Spawn 후 호출된다.\
Procedural 생성이나 Editor Tool에서 많이 사용된다.

---
5. PreInitializeComponents
```cpp
virtual void PreInitializeComponents() override;
```
Component 초기화 직전\
특수 초기화용

---
6. UActorComponent::InitializeComponent

Actor가 보유한 모든 Component에 대해 호출된다.

---
7. PostInitializeComponents
```cpp
virtual void
PostInitializeComponents()
override;
```
모든 Component 초기화 완료\
Component끼리 연결, Delegate Bind

---
8. BeginPlay
```cpp
virtual void BeginPlay() override;
```
Actor가 실제 플레이를 시작할 때 호출된다.\
Delegate Bind, Widget 생성, Timer 시작, 초기화등 

---
9. Tick\
매 프레임 호출
```cpp
virtual void Tick(float DeltaTime) override;
```
---

### Destroy
Actor를 제거. 바로 사라지는 것이 아니라 언리얼이 제거를 예약하고 적절할때 제거한다.

---
10. EndPlay
```cpp
virtual void EndPlay(
    const EEndPlayReason::Type
);
```
Actor가 월드에서 플레이 종료될 때 호출된다.\
호출 예시 Destroy, Level 변경,PIE 종료, Streaming Out\
Delegate 해제, Timer 정리 등 게임 플레이 리소스 정리에 사용한다.

---

11. Destroyed
```cpp
virtual void Destroyed();
```
Destroy() 직후\
Destroy() 처리 과정에서 호출된다.\
일반적인 정리는 EndPlay에서 처리하고, Destroy 흐름에 특화된 처리가 필요할 때.

---
12. UninitializeComponents\
각 Component의 UninitializeComponent()가 호출되어 종료 정리가 이루어진다.

---
13. BeginDestroy
```cpp
virtual void BeginDestroy();
```
GC가 메모리 해제 직전에 호출\
리소스 정리. 게임 코드에서 거의 안 씀

---
14. FinishDestroy
```cpp
virtual void FinishDestroy() override;
```
최종 파괴 단계\
일반 게임 코드에서는 사용하지 않는다.