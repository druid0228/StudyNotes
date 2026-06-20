# APlayerController

### Inheritance Hierarchy
AActor -> AController -> APlayerController

---

플레이어의 조종자. 입력, 카메라, ui 등을 담당\
서버에는 모두의 것, 클라이언트는 본인 것만 존재.

Pawn과의 관계
```
PlayerController  
        ↓ Possess
      Pawn        
```
한 시점에 하나의 Pawn만 Possess 가능하다.\

예 차량에 탑승시에 car pawn possess\
pawn이 죽거나 바뀌어도 PlayerController는 유지\
플레이어의 뇌나 영혼으로 비유할 수 있다.

추가:
Enhanced Input 연결쪽 input binding은 character에서 많이 하지만. mapping context 등록은 controller에서 많이함

템플릿한 코드
```cpp
ULocalPlayer* LocalPlayer = GetLocalPlayer();

UEnhancedInputLocalPlayerSubsystem* Subsystem =
    LocalPlayer->GetSubsystem<UEnhancedInputLocalPlayerSubsystem>();
```

추가: PlayerController는 GameState의 PlayerArray에 저장되지 않는다.
PlayerArray에는 PlayerState가 저장된다.