# Designated Initializers

## C++20


멤버의 이름을 지정해서 초기화하는 기능

```cpp
struct Player
{
    int Level;
    int HP;
    int MP;
};

Player P{.Level = 10, .HP=100,.MP=50};
```

기존의 초기화 방식의 문제
```cpp
Player P{10,100,50};
```

순서가 헷갈릴 수 있음

주의: 선언 순서대로 사용해야함

주의: 주의: Aggregate 타입에서만 사용할 수 있다.
사용자 정의 생성자 등이 있으면 사용할 수 없다.