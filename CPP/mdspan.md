# Mdspan

## C++23


Multi-Dimensional Span\
다차원 View

데이터를 소유하지는 않는다.

std::span이 1차원 View였다면,\
std::mdspan은 N차원 View

기존의 문제:
```cpp
int Data[3][4];

std::vector<std::vector<int>> Data;
```
둘 다 장단점이 있다.\
배열은 크기가 고정이다.\
vector는 유연하지만 Row1 -> Heap, Row2 -> Heap\
각 Row가 각각 별도의 메모리를 할당받는다.\
따라서 전체 2차원 데이터가 하나의 연속된 메모리라는 보장이 없다.

실제 대부분의 데이터는 연속 적인 경우가 많다.\
예: 이미지, 행렬, Tensor 등등

실제 데이터를 1차원 메모리로 저장해도\
접근하려면 Buffer[y * width + x]같은 계산을 해서 불편하다.

실제 사용:
```cpp
std::vector<int> Buffer(12);

std::mdspan View(Buffer.data(),3,4);
```
3행 4열의 데이터 처럼 사용 가능하다.

접근 View(1,2) 라면 1행 2열 내부적으로는 Buffer[1 * 4 + 2]