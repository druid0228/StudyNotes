# Enhanced Input

### Inheritance Hierarchy
UObject -> UDataAsset -> UInputAction

UObject -> UDataAsset -> UInputMappingContext

UObject -> UActorComponent -> UInputComponent -> UEnhancedInputComponent
---

Enhanced Input은 플레이어의 입력을 Action 단위로 추상화하는 입력 시스템이다.

### 구성 요소
```
Player Input
↓
Input Mapping Context
↓
Input Action
↓
PlayerController / Pawn
```

### Input Action
하나의 행동(Action)을 정의하는 Data Asset\
예
* Move
* Look
* Jump
* Fire
Action은
- Value Type
- Trigger
- Modifier
등의 설정을 갖는다.

`Value Type`\
Bool, Axis1D, Axis2D, Axis3D
```cpp
void Move(const FInputActionValue& Value)
{
    FVector2D Input = Value.Get<FVector2D>();
}
```
Action의 Value Type과 같은 타입으로 가져와야 한다.

`Trigger Event`
* Started : 처음 눌렀을 때 한 번
* Triggered : 조건이 만족되는 동안 계속
* Completed : 입력이 정상적으로 끝났을 때
* Canceled : 도중에 취소되었을 때

`Modifier`\
입력 값을 변환한다.\
예: Negate, Swizzle Input Axis, Scalar



### Input Mapping Context
Action과 실제 입력 장치를 연결한다.
```
Move Action
    ↓
WASD
Left Stick

Jump Action
    ↓
Space
A Button
```
같은 Action이라도 Mapping Context를 바꾸면 다른 키를 사용할 수 있다.

### EnhancedInputComponent
입력을 바인딩하는 Component
```cpp
EnhancedInputComponent->BindAction(
    MoveAction,
    ETriggerEvent::Triggered,
    this,
    &ThisClass::Move
);
```
Action이 발생하면 지정한 함수를 호출한다.

### 입력 흐름
```
Keyboard / Gamepad
↓
Enhanced Player Input
↓
Input Mapping Context
↓
Input Action
↓
EnhancedInputComponent
↓
Move()
Jump()
Fire()
```

---
추가: 여러 IMC를 동시에 사용할 수 있다.\
```
Priority 100
UI IMC

Priority 10
Gameplay IMC
```
동일한 입력이 겹치면 Priority가 높은 IMC가 우선 적용된다.\
IMC를 AddMappingContext()할 때 Priority를 지정한다.
