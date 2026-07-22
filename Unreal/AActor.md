# AActor

### Inheritance Hierarchy
UObject -> AActor

---

`AActor`는 World에 존재할 수 있는 모든 객체의 기본 클래스이다.

- 월드에 배치될 수 있다.
- Spawn할 수 있다.
- Tick가능하다.
- Component를 가질 수 있다.

Actor는 반드시 하나의 World에 속한다.


### Owner
Actor는 다른 Actor를 Owner로 가질 수 있다.
```cpp
GetOwner()
```

### Attach
Actor를 다른 Actor나 Component에 붙일 수 있다.
```cpp
AttachToActor()
AttachToComponent()
```

### Owner와 Attach의 차이
| 구분    | Owner                      | Attach                                   |
| ----- | -------------------------- | ---------------------------------------- |
| 의미    | **논리적인 소유 관계**             | **공간적인 부모-자식 관계**                        |
| 위치 영향 | ❌ 없음                       | ✅ 부모 Transform을 따라감                      |
| 목적    | 권한, 팀, 데미지, 생성자 추적         | 물체를 붙여서 함께 움직임                           |
| 함수    | `SetOwner()`, `GetOwner()` | `AttachToActor()`, `AttachToComponent()` |

Owner은 누가 소유하는가 Attach는 누구에게 붙어있는가


### RootComponent
Actor의 기준이 되는 Component

```cpp
GetRootComponent()
```

### 대표적인 Actor
```
AActor
    ├── APawn
    ├── ACharacter
    ├── APlayerController
    ├── AGameModeBase
    ├── AGameStateBase
    ├── AHUD
    ├── AStaticMeshActor
    └── ...
```