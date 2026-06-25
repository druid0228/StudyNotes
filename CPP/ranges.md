# Ranges

## C++20


컨테이너나 범위(range)를 더 편하고 안전하게 사용하기 위한 라이브러리

```cpp

std::vector<int> Number

std::sort(Number.begin(),Number.end()); // 불편함

std::ranges::sort(Number)
```
begin 과 end를 알아서 찾아준다.

```cpp
std::equal(A.begin(),A.end(),B.begin()) => std::ranges::equal(A,B)
std::find(V.begin(),V.end(),10) => std::ranges::find(V, 10)
std::binary_search(V.begin(),V.end(),10); => std::ranges::binary_search(V,10);
```

다음과 같은 실수도 막을 수 있다.
```cpp
std::sort(
    Numbers.begin(),
    Other.end());
```

Projection 기능

```cpp
std::ranges::sort(
    Players,
    std::less{},
    &Player::Level);

```
멤버 하나를 꺼내서 비교 가능하게 해줌

```cpp
[](const Player& P)
{
    return P.Level + P.HP;
}
```
이것도 가능

기존 compare 함수 넘기는 것과 비교
```cpp
 [](const Player& A, const Player& B)
    {
        return A.Level < B.Level;
    }
```
두개의 값을 비교하는 함수를 작성

projection은 비교할 값을 꺼내는 방법을 정의

장점 함수 안에서 비교 연산자를 바꾸는 것이 아니라\
std::less{}, std::greater 등으로 인자로 바로 바꿀 수 있음\
멤버 하나의 경우 함수가 훨씬 단순해짐