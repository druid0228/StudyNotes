# Lambda Template Parameters

## C++20


기존 람다
```cpp
[](auto value)
{
}
```
내부적으로는 컴파일러가
```cpp
template<typename T>
void operator()(T value)
{
}
```
처럼 만들어주지만 하지만 템플릿 매개변수(T)를 명시할 수 없어\
std::is_integral_v<T>, requires, concept 등을 직접 사용할 수 없었다.
```cpp
[](auto value)
{
    if constexpr (std::is_integral_v<T>)
    {
    }
}
```
템플릿 관련 함수들을 사용할 수 없었다.\
타입 검사, Concept, 등등..


C++20 부터 lambda도 템플릿 매개변수를 명시적으로 선언할 수 있다.
```cpp
auto lamb = []<typename T>(T value)
{
    if constexpr (std::is_integral_v<T>)
    {
        std::cout << "Integral";
    }
};
```
