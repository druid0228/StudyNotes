# to_underlying

## C++23

`std::to_underlying` enum class의 기반 타입 값을 반환한다.

static_cast<underlying_type>을 더 읽기 쉽게 만든 함수이다.
```cpp
enum class EState : uint8_t
{
    Idle = 0,
    Run = 1,
    Jump = 2
};

// Value == 1
auto Value = std::to_underlying(EState::Run);

// static_cast<uint8_t>(EState::Run)과 값은 같다.
```

static_cast로 타입을 cast해도 결과는 같지만,\
이것이 타입의 값을 얻기 위해서인지 다른 타입인데 cast 하는 것인지\
의도가 명확하지 않다.\
그러므로 enum의 기반 타입 값을 얻을 때는 std::to_underlying를 사용하는 것이 좋다.

std::underlying_type_t는 타입 자체를 얻는 차이가 있다.
```cpp
using Type = std::underlying_type_t<EState>;

// Type == uint8_t (Type 자체가 타입)
```

주의: std::to_underlying의 인자는 enum만 받을 수 있다.

