# Input System

1. Old Input System (Legacy)
2. New Input System


## Old Input System
```C#
void Update()
{
    if (Input.GetKeyDown(KeyCode.Space))
    {
        Jump();
    }

    float x = Input.GetAxis("Horizontal");
}
```
코드에서 직접 키를 처리하는 방식

## New Input System
입력을 하드코딩하지 않고 PlayerInputActions.inputactions Asset에 정의
```
Input Device
    ↓
Binding
    ↓
Input Action
    ↓
Player 코드
```

### 상태 구분
```
started
performed
canceled
```
상태를 구분하여 연결할 수 있다.

### Action Map
Action들을 그룹으로 묶는다
예

```
Player

Move
Jump
Attack
```
```
UI

Navigate
Click
Submit
```
```
Vehicle

Accelerate
Brake
Steer
```

필요에 따라 Action Map을 켜고 끌 수 있다.

예: Player를 사용하다 esc 누르고 ui 쓰는 곳에서 UI Action map