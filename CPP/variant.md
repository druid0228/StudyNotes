# variant

## C++17

`std::variant`는 여러 타입 중 하나를 저장하기 위해 나왔다.

가장 큰 타입의 저장공간과 현재 활성 타입 정보를 가진다.

컴파일 타임에 가능한 타입 집합이 고정된다.

union의 장점(여러 타입 중 하나 저장)은 유지하면서\
타입 안전성을 제공하기 위해 만들어졌다.

```cpp
#include<variant>

std::variant<int,float,std::string> Value;
```

값 저장
```cpp
Value = 10;
Value = 3.14f;
Value = "Hello";
```
항상 하나만 저장되고 새로운 값을 저장하면 기존 객체는 자동으로 파괴되고
새로운 객체가 생성된다.

어떤 타입인지 확인
```cpp
std::holds_alternative<int>(Value)
```

값 꺼내기
```cpp
int Number = std::get<int>(Value);
```
현재 활성 타입과 다른 타입으로 get()을 호출하면 `std::bad_variant_access` 예외 발생한다.

안전하게 가져오기 `std::get_if`
```cpp
if (int* Number = std::get_if<int>(&Value))
{
    std::cout << *Number;
}
```
실패하면 nullptr을 반환한다.\

현재 저장된 타입 번호 `variant.index()`
```cpp
std::variant<int,float,string> Value;

Value = 3.14f;

std::cout << Value.index(); // 예시 결과 1
/*
    0 -> int
    1 -> float
    2 -> string
*/
```

### std::visit

컴파일 시 가능한 타입별 코드를 생성하고,\
런타임에 현재 활성 타입에 맞는 함수를 호출한다.

타입마다 서로 다른 처리가 필요할 때 사용한다.
```cpp
std::visit(
[](auto&& Value)
{
    std::cout << Value;
}, Variant);
```

람다 대신 구조체도 사용 할 수 있다.

```cpp
struct Visitor
{
    void operator()(int Value) const
    {
    }

    void operator()(float Value) const
    {
    }

    void operator()(const std::string& Value) const
    {
    }
};

std::visit(Visitor{}, Variant);
```


### variant vs any

variant
- 저장 가능한 타입을 컴파일 타임에 지정
- 일반적으로 std::any보다 성능이 좋다.

any
- 어떤 타입이든 저장 가능
- 타입은 런타임에 확인