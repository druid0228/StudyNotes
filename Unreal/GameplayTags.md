# Gameplay Tags

객체를 식별하거나 상태를 표현하기 위한 계층형 시스템.\
문자열보다 안전하고 효율적으로 객체를 분류하기 위해 사용한다.

객체, 상태, 능력 등을 계층적으로 분류하기 위한 시스템.

생성 예시:
UE_DEFINE_GAMEPLAY_TAG(TAG_Item_Weapon,"Item.Weapon")

### FGameplayTagContainer
태그를 여러 개 저장한다.

함수들
- `Tags.AddTag()`
- `Tags.RemoveTag()`
- `Tags.HasTag()`

### MatchesTag
계층의 부모 태그와도 비교할 수 있다.
```
Item

└── Weapon

    └── Sword
```
```cpp
SwordTag.MatchesTag(ItemTag) == true
```

### Gameplay Tag 비교
Gameplay Tag
* 런타임 확장 가능
* 여러 개 보유 가능
* 계층 구조 지원

Enum
* 컴파일 타임에 고정
* 하나의 값

Actor Tag
* 단순한 문자열
* 계층 없음


