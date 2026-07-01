# UE Object References

| 보유 형태               | 의미              | 대상            | 대표 사용처                |
| ------------------- | --------------- | ------------- | --------------------- |
| `TObjectPtr<T>`     | UObject 강한 참조   | UObject       | Component, Widget     |
| `TWeakObjectPtr<T>` | UObject 약한 참조   | UObject       | 캐시, Observer          |
| `TSoftObjectPtr<T>` | 에셋 경로 참조        | UObject Asset | Texture, Mesh         |
| `TSubclassOf<T>`    | 클래스 보유          | UClass        | Spawn, GameplayEffect |
| `TSoftClassPtr<T>`  | 클래스 경로          | UClass        | BP Class 지연 로드        |
| `TSharedPtr<T>`     | 공유 소유           | 일반 C++        | GAS Spec, Slate       |
| `TWeakPtr<T>`       | SharedPtr 약한 참조 | 일반 C++        | 순환 참조 방지              |
| `TUniquePtr<T>`     | 단독 소유           | 일반 C++        | Helper 객체             |


UObject를 갖고있냐 일반 C++ 객체를 갖고 있냐에 따라 우선 구분한다.


### TObjectPtr<T>

실제 객체를 가리킨다.\
*를 사용하지 않고 존재하는 이유.\
GC의 참조 추적을 더 안전하게 하고,
에디터 및 디버깅을 지원하기 위해 사용한다.

멤버 변수를 보관할때 대표적으로 사용\
함수 매개변수는 *를 많이 쓴다.

비교: TSubclassOf는 객체가 아닌 설계도를 보관

### TSubclassOf<T>

T를 상속한 UClass를 보유\
객체를 보관하는 것이 아니라,나중에 생성할 클래스 정보를 보관한다.\
나중에 생성할 클래스를 저장하는것이다.

예시: TSubclassOf<GameEffect>\
플레이어 A, 플레이어 B, 몬스터 C가 실제로 어떤 Effect를 갖고 있을지 모름\
에디터등에서 수정 가능하도록

### TWeakObjectPtr<T>

UObject를 가리키지만 소유하지 않는 참조.\
이 객체를 어떤 관계로 가지고 있느냐가 핵심.\
내것이나 오래 관리한다면 TObjectPtr, 남의 객체를 잠깐 바라보는 참조 TWeakObjectPtr

예시:  
TObjectPtr<AEnemy> Enemy; 에너미가 죽어서 삭제되었을때 잘못 사용하면 크래시 위험\
TWeakObjectPtr<AEnemy> Enemy; 삭제되면 Weak는 자동으로 무효

참고: shared_ptr는 "참조 개수"를 센다.\
    TObjectPtr는 "GC가 따라갈 참조"를 등록한다.\
    TWeakObjectPtr는 GC가 따라가지 않는 참조지만, 객체가 살아있는지는 확인할 수 있다.\


### TSoftObjectPtr<T>

TObjectPtr는 실제 객체가 메모리에 로드되어 있어야 한다.\
반면에 TSoftObjectPtr은 경로만 기록한다.\
에셋에 관련된 곳에 주로 사용된다.

예시: 아직 등장안한 Dragon.uasset 크기 250MB 사용하지 않고 있는데 메모리에 올려두는 것은 낭비\
필요할 때 로드 후 객체 사용.

추가: TWeak도 실제 메모리에 있는 것을 가리키는 것이다.


### TSoftClassPtr<T>

클래스를 들고 있는 것이 아니라 경로를 들고 있는다.

예시: 아직 사용 안하고 있는 UI. 상점에 들어가야지만 나오는 상점 UI


### TSharedPtr<T>

참조 카운트 기반\
공유 소유\
UObject가 아닌 C++ 객체들에 대해 사용.

MakeShared가 메모리효율, 예외 안정성, 성능이 좋으므로 new보다 권장.

예: struct FGameplayEffectSpec

### TWeakPtr<T>

RefCount 증가 안 함\
사용이유 순환 참조

순환 참조 예:
```
Player
↓
Inventory

Inventory
↓
Player
```
서로 카운트를 잡고 있어서 0이 되지 못함\
한쪽을 TWeakPtr로 만든다.

바로 접근하지 않는다.\
예
```cpp
TWeakPtr<FMonsterData> Weak;

if (TSharedPtr<FMonsterData> Data = Weak.Pin())
{
    Data->Health;
}
```
이유: 이미 삭제되었을 수도 있다.


### TUniquePtr<T>

객체의 주인은 오직 한명이다.

Unique는 이동(Move)를 사용한다.\
예:
```cpp
TUniquePtr<FMonsterData> B =
    MoveTemp(A);
```


예:
```cpp
TUniquePtr<FMonsterData> A;

TUniquePtr<FMonsterData> B = A;
```
컴파일 에러

중요: 복사만 금지될 뿐,
다른 객체가 생 포인터나 참조로 접근하는 것은 가능하다.

참고: 위의 Pointer들과 다르게 GC와 관련이 없음