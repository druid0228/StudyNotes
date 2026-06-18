# [[nodiscard]]

## C++17

함수의 리턴값이 존재하는데 실수로 반환 하지 않았을 때를 예방 하기 위해 존재


```cpp
[[nodiscard]]
bool init()
{
    return false
}

main()
{
    Init()
}
```
위와 같은 상황을 방지하기 위함

컴파일은 되지만 Warning이 나온다.

추가:
```cpp
struct [[nodiscard]]ErrorCode
{
    ...
}
```

이와 같이 타입에도 적용된다. (enum, class 등 동일)
[[nodiscard]] 타입의 반환값을 무시하면 Warning이 발생한다.