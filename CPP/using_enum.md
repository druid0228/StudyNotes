# using enum

## C++20

기존 enum class 값을 쓸 때 마다 항상 스코프를 붙여야 해서 생기는 가독성 문제를 해결해준다.

기존
```cpp
enum class EWeaponState
{
    Idle,
    Firing,
    Reloading
};

switch (State)
{
case EWeaponState::Idle:
    break;

case EWeaponState::Firing:
    break;

case EWeaponState::Reloading:
    break;
}
```

using 사용 이후
```cpp
{
    using enum EWeaponState;
    switch (State)
    {
    case Idle:
        break;

    case Firing:
        break;

    case Reloading:
        break;
    }
}
```
훨씬 깔끔하다.

```cpp
enum class EWeaponState
{
    Idle,
    Firing
};

enum class EAIState
{
    Idle,
    Attacking
};
```

이런 경우 같은 스코프 안에서 using을 쓰면 이름 충돌이 발생하여 컴파일 오류가 날 수 있다.\
using enum은 작은 지역 스코프 안에서만 쓰는 게 좋다.

특히 switch 처럼 하나의 enum class에 대해\
여러번 사용해야 할 경우 유용하다.