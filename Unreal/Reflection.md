# Reflection

언리얼의 Reflection은 C++ 객체의 정보를 런타임에도 알 수 있도록 하는 시스템이다.

필요한 이유: Blueprint, Editor, Garbage Collection 등\
수많은 시스템이 객체에 어떤 변수와 함수가 있어야 하는지 알아야한다.

```cpp
UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()

public:

    UPROPERTY(EditAnywhere)
    int32 Health;

    UFUNCTION(BlueprintCallable)
    void Heal();
};
```
이와 같이 매크로를 사용한다.\
이 매크로를 보고 `Unreal Header Tool(UHT)`이 추가 코드를 생성한다.

### GENERATED_BODY()

UHT가 생성한 코드를 클래스 안에 삽입하는 매크로이다.

없으면 Reflection 시스템에 등록되지 않는다.


---
일반 C++과 차이

```cpp
int Health;
```
엔진은 알 수 없음
```cpp
UPROPERTY()
int Health;
```
엔진이 접근 및 활용 가능


추가: C++의 RTTI는 dynamic_cast, typeid 등 타입 식별 정도만 제공되지만,\
언리얼 Reflection은 변수, 함수, 메타데이터까지 엔진이 관리할 수 있게 해준다.