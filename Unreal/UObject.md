# UObject

### Inheritance Hierarchy
AActor -> AInfo -> APlayerState

---

언리얼 엔진의 거의 모든 객체는 UObject를 상속받는다.

```cpp
UObject
    │
    ├── UActorComponent
    ├── UWorld
    ├── UGameInstance
    ├── UUserWidget
    ├── UTexture
    ├── UMaterial
    └── ...
```

UObject가 제공하는 대표적인 기능
```
Reflection
Garbage Collection
Serialization
UObject 생성 시스템
UPROPERTY
UFUNCTION
Asset 관리
```

GC를 사용하기 때문에 일반적인 C++처럼 new를 사용하지 않는다.

NewObject<UObject>()를 사용한다.

이유 언리얼이 직접 관리하기 위해

UObject는 World에 존재하지 않는다. == 위치, tick, transform 없음

Actor가 필요 없는 데이터 객체를 만들 때 사용\
예시:\
InventoryItem, QuestData, SkillData, ...

```cpp
UCLASS()
UPROPERTY()
UFUNCTION()
```
이런 리플렉션은 전부 UObject 기반으로 동작한다.