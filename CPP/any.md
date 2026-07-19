# any

## C++17

`std::any` 복사 가능한(CopyConstructible) 임의의 타입 값을 하나 저장할 수 있는 타입이다.

```cpp
#include <any>

std::any Value;

Value = 10;
Value = 3.14;
Value = std::string{"Hello"};
```

저장할 때는 자유지만, 꺼낼 때는 원래의 타입을 정확히 알아야 한다.
```cpp
std::any Value = 10;

int Number = std::any_cast<int>(Value);
```
타입이 맞지 않으면 예외가 발생한다.\
포인터 버전(any_cast<T>(&obj))은 nullptr을 반환한다.

확인하는법
```cpp
std::any Value = 10;

if (Value.type() == typeid(int))
{
    int Number = std::any_cast<int>(Value);
}

if (int* Number = std::any_cast<int>(&Value))
{
    std::cout << *Number;
}
```

처음에 비어 있을 수 있다.\
`has_value()` 함수로 확인 가능


사용 예
```cpp
std::unordered_map<std::string, std::any> Settings;

Settings["Volume"] = 0.8f;
Settings["PlayerName"] = std::string{"Player"};
Settings["Fullscreen"] = true;
```
하나의 컨테이너에 서로 다른 타입을 저장할 수 있다.

---
`std::variant`와의 차이, variant는 저장 가능한 타입을 미리 제한\
```cpp 
std::variant<int, float, std::string> Value
```
반면 std::any는 복사 가능한 타입이면 대부분 저장\
그리고 variant는 컴파일 타임 중심, any는 런타임

---
내부적으로 std::any는 값의 실제 타입을 숨긴다.\
`std::function`도 비슷한 방식\
이러한 방식들을 Type Erasure이라고 한다.