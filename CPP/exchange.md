# Exchange

## C++14

`std::exchange`는 객체에 새 값을 대입하고, 대입하기 전의 값을 반환한다.

```cpp
#include <utility>

int Value = 10;

int OldValue = std::exchange(Value, 20);
```


이동 생성자에서 사용
```cpp
class Resource
{
public:
    Resource(Resource&& Other) noexcept
        : Handle(std::exchange(Other.Handle, nullptr))
    {
    }

private:
    void* Handle = nullptr;
};
```