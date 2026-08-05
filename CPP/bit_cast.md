# bit_cast

## C++20

`std::bit_cast` 한 타입의 비트 패턴을 유지한 채, 같은 크기의 다른 타입값으로 해석한다.

```cpp
#include<bit>

float Value = 1.0f

std::uint32_t Bits = std::bit_cast<std::uint32_t>(Value);

// static_cast<std::uint32_t>(Value) == 1
```
결과는 1.0f가 아니다.\
일반 적인 환경에서 float 1.0f의 비트: 00111111100000000000000000000000\
정수로 보면 1065353216 이다.

반면에 static_cast는 1로 변환된다.


`reinterpret_cast` 대신 쓰는 이유\
```cpp
float F = 1.0f;

std::uint32_t Bits =
    *reinterpret_cast<std::uint32_t*>(&F);
```
C++의 Strict Aliasing 규칙을 위반하므로 Undefined Behavior가 될 수 있다.

bit_cast는 이러한 변환을 위해 표준으로 제공되는 방식이다.


중요한 조건:
1. 크기가 같아야한다.\
    &nbsp;&nbsp;&nbsp;: sizeof의 크기가 같아야 가능하다.
2. 타입이 단순 복사 가능해야한다(Trivially Copyable)\
    &nbsp;&nbsp;&nbsp;: std::string 과 같은 복잡한 객체는 적절하지 않다.
3. 결과 숫자는 시스템의 Endianness에 영향을 받을 수 있다.\
    &nbsp;&nbsp;&nbsp;: 네트워크 파일이나 파일 포맷에서 사용할 때는 bit_cast 이후 바이트 순서도 고려해야한다.


구조체에도 사용 가능하다.\
예시

```cpp
struct ColorBytes
{
    std::uint8_t R;
    std::uint8_t G;
    std::uint8_t B;
    std::uint8_t A;
};

static_assert(sizeof(ColorBytes) == sizeof(std::uint32_t));

ColorBytes Color{255, 128, 0, 255};

std::uint32_t Packed =
    std::bit_cast<std::uint32_t>(Color);
```


유용한곳:
* 부동소수점 비트 분석
* 해시
* 직렬화 구현
* 특수값 확인
* 그래픽스 데이터 변환