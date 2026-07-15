# Function

## C++11

std::function 나온 이유 람다를 포함한 다양한 Callable 객체를 저장하기 위해 사용한다.

```cpp
std::function<ReturnType(Args...)>
```

기존의 C 스타일\
함수 포인터를 사용했다.
```cpp
void Execute(void(*Func)())
{
    Func();
}

Execute(Print);

auto Lambda = [this]()
{
};

Execute(Lambda);// 캡쳐 있는 람다는 불가능
```
문제는 캡처가 있는 Lambda는 함수 포인터로 변환되지 않는다.

그래서 나온 것이 std::function\
Callable 객체를 내부에 보관한다.\
일반함수,람다,함수객체,멤버함수 상관없이 저장 가능하다.

예:
```cpp
std::function<int(int)> Square =
[](int x)
{
    return x * x;
};

std::cout << Square(5);
```

중요 예:
```cpp
int HP = 100;
std::function<void()> F =
[HP]()
{
    std::cout << HP;
};
HP = 200;
F();    // F = 100
```
```cpp
std::function<void()> F =
[&HP]()
{
    std::cout << HP;
};
HP = 200;
F();    // F = 200
```

std::function은 템플릿보다 느릴 수 있다.\
어떤 Callable이 들어있는지 런타임에 관리한다.\
컴파일 타임에 타입이 결정되지 않는다.\
타입소거, 간접호출, 힙할당 등의 오버헤드 생길수 있다.


## C++23

### std::move_only_function

`std::function`은 함수,람다,functor 등을 저장할 수 있었다.\
하지만 조건은 복사 가능(Copyable)해야 했다.


문제:
```cpp
auto Ptr = std::make_unique<int>(100);

auto Lambda =
[P = std::move(Ptr)]
{
};
```
unique_ptr는 복사 금지이다.
```cpp
std::unique_ptr<int> A;
std::unique_ptr<int> B = A; // ❌
```

그러면 람다도 복사 금지 객체가 된다.\
왜냐하면 unique_ptr을 갖고 있기 때문에

그래서 나온것이 move_only_function\
이동만 가능하다.

```cpp
auto Ptr = std::make_unique<int>(100);

std::move_only_function<void()> Func =
[P = std::move(Ptr)]
{
    std::cout << *P;
};
```


필요한 중요 이유: Modern C++에서는 move-only 객체가 많다
- std::unique_ptr
- std::promise
- std::packaged_task
- Coroutine Handle
등등

이런 객체를 람다가 캡쳐하면 람다도 move-only가 되기 때문에\
이전에 std::function에 저장할 수 없었던 것을 해결하기 위해\
std::move_only_function 이 나온 것이다.
