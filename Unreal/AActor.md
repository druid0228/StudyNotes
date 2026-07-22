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