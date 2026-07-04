# if consteval

## C++23

현재 함수가 컴파일 타임 평가 중인지 확인하는 문법.

constexpr 함수는 컴파일 타임과 런타임 모두에서 호출될 수 있다.

예시
```cpp
constexpr int Foo(int X)
{
    if consteval
    {
        return X * 2;
    }
    else
    {
        return X + 2;
    }
}
```

## C++20

이전에 사용하던 방식
```cpp
#include<type_traits>

if(std::is_constant_evaluated())
// C++23에서는 `if consteval`이 추가되어 같은 목적을 더 명확하게 표현할 수 있다.

```