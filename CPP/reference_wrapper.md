# reference_wrapper

## C++11

`std::reference_wrapper` 참조를 객체처럼 저장하고 복사할 수 있게 해준다.


```cpp
// 일반적인 참조
int A = 10;
int& Ref = A; 

// 참조는 컨테이너에 저장할 수 없다.
std::vector<int&> Vec; // X

std::reference_wrapper<int> Ref = A;

std::vector<reference_wrapper<int>> Vec; // 가능하다.
```
컨테이너는 참조를 저장할수 없지만\
reference_wrapper는 저장 가능하다.


참조를 변경 할 수 있다.
```cpp
std::reference_wrapper<int> Ref = A;

Ref = B
```
A를 참조에서 B를 참조로 변경 가능하다.


Wrapper는 일반적으로 내부에 포인터를 이용해 참조를 구현한다.\
Reference 처럼 행동하지만 복사, 대입, 컨테이너 저장 등이 가능하다.


보통
```cpp
auto Ref = std::ref(A);
auto CRef = std::cref(A);
```
로 사용한다. 타입은 std::reference_wrapper<T> 이다.

읽기 전용은 std::reference_wrapper<const T> 이다.