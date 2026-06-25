# Views

## C++20


데이터를 복사하지 않고 원하는 형태로 보여주는 기능.


예
```cpp
auto Even =
    Numbers
    | std::views::filter(
        [](int N)
        {
            return N % 2 == 0;
        });
```
Even은 벡터가 아니고 view

체인

```cpp
auto Result =
    Numbers
    | std::views::filter(
        [](int N)
        {
            return N % 2 == 0;
        })
    | std::views::transform(
        [](int N)
        {
            return N * 10;
        });
```

벡터를 만드는것이 아니라 필요한 값에 접근할때 계산하여 준다. Lazy Evaluation

저장하지 않고 순회 시점에 계산하므로, 같은 view를 여러 번 순회하거나 transform/filter가 무거우면
반복 계산으로 인한 성능 손해가 생길 수 있다. 주로 한번의 순회로 끝나는 행동에 대해서 유용하다.


std::views:: 함수들\
```cpp
filter              // 조건에 맞는 원소만 통과
transform           // 원소를 다른 값으로 변환
take                // 앞에서 N개만
drop                // 앞에서 N개 버림
reverse             // 역순으로 보여줌
keys , values
split
...

C++23
zip
adjacent
slide
chunk
stride
```

기본적으로 복잡한 연산(여러개의 원소를 참조하는)들은 제공되지 않고\
대체로 순회하면서 처리할 수 있는 동작들을 제공한다. view의 목적에 적합.


Number | std::views::filter(...)\
형태로 되어 있는데 std::views::filter 같은 객체는 View Adaptor Object이고,
| 연산자를 이용해 View를 연결(체이닝)하여 사용할 수 있다.

예 std::views::filter는 일종의 View Adaptor Object다\
std::views는 이러한 객체들을 갖고있다.


 