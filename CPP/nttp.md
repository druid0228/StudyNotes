# Non-Type Template Parameter (NTTP)

## C++17

템플릿에서 타입이 아니라 값을 받는 것
```cpp
template<typename T>
```
일반적인 형태\
T에 타입이 들어간다 int, float, string ...

반면
```cpp
template<int N>
void Print()
{
    std::cout << N;
}

Print<5>();
```
처럼 타입 대신 값이 들어간다.

반드시 컴파일 타임 상수여야 한다.
```cpp
int x = 10;

Print<x>(); // 불가능

// 가능한 예
constexpr int Size = 128;
Print<Size>();

enum class Color { Red };
Print<Color::Red>();

```


## NTTP with auto

기존에는 NTTP 타입을 직접 적었어야 했다. C++ 17부터 auto 사용이 가능하다.
```cpp
template<auto Value>
void Print()
{
    std::cout << Value;
}

Print<10>();
Print<'A'>();
Print<true>();
```

enum도 pointer도 가능하다.


실제 라이브러리 사용 예시
```cpp
std::array<int, 10>

template<class T, std::size_t N>
class array;
```
여기서 int는 타입 10은 nttp

컴파일 상수를 받을 때 사용
```cpp
template<auto Size>
class Buffer
{
};

Buffer<128> A;
Buffer<256> B;
```


NTTP의 핵심 값을 타입의 일부로 만들기 위한 기능\
예를들어 Buffer<128>과 Buffer<256>은 타입이 다르다\
그러므로 A=B 같은 대입은 컴파일이 불가능하다.