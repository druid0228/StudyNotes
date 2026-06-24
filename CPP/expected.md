# Expected

## C++23

성공 결과 또는 에러를 담는 타입.

std::expected<T,E>

E는 보통 string, enum class 등 에러를 설명 할 수 있는 타입 종류.\
int나 float도 E 부분에 넣을 수 있지만 이상함.

예시
```cpp
std::expected<int, std::string>
Divide(int A, int B)
{
    if (B == 0)
    {
        return std::unexpected(
            "Divide By Zero");
    }

    return A / B;
}
```
성공하면 int 실패하면 string

```cpp
auto Result = Divide(10,0);

Result.value()
Result.error()

if(Result)
if(!Result)
방식 가능

```

Result의 타입은 std::expected<int, std::string>이다.\
내부에 operator bool()이 구현되어 있을 뿐.\
주의 if(Result)는 if(Result.has_value()) 이다.\
내부가 expected<bool,string>일 때 value가 true인지 false인지 값이 아니라.\
value가 있는지 error인지의 체크임. 값으로 조건을 하고 싶다면 if(*Result) 혹은 if(Result.value())


optional과 비교
```cpp
std::optional<int> Divide(int A, int B)
{
    if (B == 0)
        return std::nullopt;
    return A / B;
}
auto Result = Divide(10, 2);

if (Result)
{
    std::cout << *Result;
}
```
위와 같이 가능 문제는 왜 실패했는지 알 수 없음.

optional은 비어있는 상태 std::nullopt가 존재하지만.\
expected는 비어있는 상태는 error 객체(E)를 갖고 있게 됨