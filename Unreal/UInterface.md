# UInterface

Interface가 이미 존재하는데 필요한 이유\
언리얼의 리플렉션 및 내부 사용을 하기 위해

언리얼 내부에서 사용할 때
```cpp
// 인터페이스 구현되어있으면 포인터 그렇지 않다면 nullptr
IDamageable* Damageable =
    Cast<IDamageable>(Actor);

//리플렉션 기준으로 검사
Actor->GetClass()->ImplementsInterface(
    UDamageable::StaticClass()
);
```

예시 
```cpp
// Reflection용
UINTERFACE(BlueprintType)
class UInteractable : public UInterface
{
    GENERATED_BODY()
};

// 실제 인터페이스
class IInteractable
{
    GENERATED_BODY()

public:

    virtual void Interact(AActor* Instigator) = 0;
    virtual FString GetInteractText() const = 0;
    virtual bool CanInteract() const = 0;
};
```
Reflection용에는 딱 저것만 존재하면 된다 언리얼에 등록하는 용도.\
기준은 UINTERFACE() 아래 GENERATED_BODY 2개를 묶어서 인식한다.\
보통은 편하게 이름을 맞춰쓴다 UISome - IISome

## Interface 호출

Execute_XXX



예시
```cpp
class ADoor
    : public AActor
    , public IInteractable
{
public:
    virtual void Interact() override;
};

```

일반 C++에서만 사용한다면
```cpp
if (IInteractable* Interactable =
    Cast<IInteractable>(HitActor))
{
    Interactable->Interact();
}
```

문제는 C++에서 구현될 수도 있지만 Blueprint에서도 구현될 수 있다.

```cpp
IInteractable::Execute_Interact(HitActor);
```
로 호출하면 언리얼이 내부적으로\
Blueprint에 구현이 있으면 Blueprint Event 호출을\
없으면 C++ 구현을 호출하게 된다.

확실하게 C++에서만 사용되는게 보장된 경우에는 C++ 방법으로만 호출하지만\
보통의 상황에서 Blueprint에서 호출될 수도 있기 때문에\
언리얼은 Execute_XXX 방식의 호출을 권장한다.

약간의 오버로드는 생긴다.


1. BlueprintNativeEvent
```cpp
UFUCTION(BlueprintNativeEvent)
void Interact();
```
기본 구현은 C++에서 필요하면 Blueprint가 override 가능

실제 Interface를 갖는 객체에서의 구현
```cpp
void AChest::Interact_Implementation()
{
    OpenChest();
}
```
중요한것은 구현에 _Implementation이 붙는다.

2. BlueprintImplementableEvent
```cpp
UFUCTION(BlueprintImplementableEvent)
void Interact();
```
C++ 구현 없음 반드시 Blueprint에서 구현