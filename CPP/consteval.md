# Consteval

## C++20

반드시 컴파일 타임에 평가(evaluate)되어야 하는 함수.\
constant evaluation

프로그램 실행중에 호출 되면 안되고\
컴파일 타임에 반드시 계산되어야 하는 경우 사용한다.\
라이브러리, 메타프로그래밍, 컴파일타임생성기 등


```cpp
consteval int Square(int x)
{
    return x*x;
}

constexpr int A = Square(3);
...
// 컴파일 에러
int X = 10;
int B = Square(X);
```

대부분의 상황에서는 constexpr을 사용한다.

constexpr은\
컴파일 타임이면 컴파일 타임\
런타임이면 런타임 둘다 가능

constexpr: const expression

