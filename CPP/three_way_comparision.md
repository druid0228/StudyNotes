# Three-way Comparison

## C++20

비교 연산자를 한번에 생성하는 연산자.

요약 <=> 연산자 하나로 > < == 등 비교하는 모든 연산자를 구현 가능하다.
A<B, A>B, A==B, A<=B ... 따로 만들 필요 없이 operator<=>를 정의하면 비교 연산자들을 자동 생성할 수 있다.


```cpp

struct Player
{
    int Level;
    int HP;
}
비교하려면

bool operator==(const Player&) const;
bool operator!=(const Player&) const;
bool operator<(const Player&) const;
bool operator<=(const Player&) const;
bool operator>(const Player&) const;
bool operator>=(const Player&) const;

필요 한경우 전부 작성해야 하는 귀찮음


Three-way Comparision

auto operator<=>(const Player&) const = default;

끝
```

결과 타입은 std::strong_ordering

주의 : operator<=> default 를 사용하면 모든 멤버 변수가 비교 가능해야함.\
그런 경우 컴파일러가 자동으로 순서대로 비교하도록 만들어줌.\
```cpp
struct Player
{
    int Level
    std::mutex lock
}
```
컴파일 오류


본문 예 default 비교는 멤버 선언 순서대로 비교한다.
Level이 같으면 HP를 비교한다.


직접 구현시
```cpp
    std::strong_ordering operator<=>(const Player& Other) const
    {
        return Level <=> Other.Level;
    }

    이런 총합 비교도 가능
    auto operator<=>(const Player& Other) const
    {
        return (HP + MP) <=> (Other.HP + Other.MP);
    }

    같았을 때 다음 멤버 비교도 가능
    auto operator<=>(const Player& Other) const
    {
        if (auto Result = HP <=> Other.HP;
            Result != std::strong_ordering::equal)
        {
            return Result;
        }
        return MP <=> Other.MP;
    }
```

예
```cpp
Player A,B
A < B   =>  (A<=>B) < 0
```

