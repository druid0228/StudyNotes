# Concept & Requires

## C++20

템플릿 인자의 조건을 명시하는 문법\
조건이 존재하지 않으면 컴파일러가 템플릿 내부에 들어가서야 오류를 발견하고 아주 긴 템플릿 에러를 뱉는다.\

이전에 복잡한 enable_if 문법을 대체하기 위해 나왔다. 이전의 문법은 아주 복잡하다.
```cpp
template<typename T>
requires std::integral<T>

            vs

template<typename T,std::enable_if_t<std::is_integral_v<T>>=0>
```

여러 조건도 가능하다
```cpp
template<typename T>
requires
    std::integral<T> &&
    sizeof(T) >= 4
```


추가 많이 쓰이는건 축약형
```cpp
template<std::integral T>
```

concept 로 직접 타입 조건을 만들 수 있다.\
컴파일 타임에 평가되는 bool 값이다.

예
```cpp
template<typename T>
concept Number =
    std::integral<T> ||
    std::floating_point<T>;
```
조건식 정수거나 floating 이거나.



```cpp
template<typename T>
concept DamageType =
requires(T t)
{
    t.Damage;
};


```
T의 멤버에 Damage가 존재한다면 true 그렇지 않다면 false

requires는 bool 값을 생성하는 컴파일 타입 검사식.

또다른 예

```cpp
template<typename T>
concept Addable =
requires(T a,T b)
{
    a+b;
};

template<Addable T>
T Add(T a,T b)
{
    return a+b;
}

```

반환 타입 검사도 가능
```cpp
template<typename T>
concept Drawable =
requires(T t)
{
    { t.Draw() } -> std::same_as<void>;
};
```

requires와 concept를 조합하면
내가 원하는 타입 조건을 만들 수 있다.

requires는 컴파일러가 특정 식(Expression)이 유효한지 검사하여 true/false를 반환한다. 이 안에서는 특별 취급 되어 컴파일 에러를 뱉지 않는다.


추가: concept 기본 예
```cpp
std::integral<T>
std::floating_point<T>
std::same_as<T,U>
std::derived_from<T,U>  // chracter, actor -> true
std::is_base_of<U,T>  // actor, character
std::convertible_to<T,U>   // int, float -> true
std::constructible_from<T,Args...> // FString, const char*
std::default_initializable<T>   // Type{}
std::equality_comparable<T>
std::totally_ordered<T> // < <= > >= 전부 가능한지 체크
std::invocable<F,Args...>   // Lambda(10)
std::copyable<T>
std::movable<T>
std::regular<T> //  copy + move + ...
std::ranges::range<T>   // begin end
std::ranges::sized_range<T> // size
std::ranges::contiguous_range<T>
```

추가: 이전 방식으로 구현할 경우
```cpp
template<typename T, typename = void>
struct HasDraw : std::false_type
{
};

template<typename T>
struct HasDraw<
    T,
    std::void_t<
        decltype(
            std::declval<T>().Draw()
        )
    >
>
: std::true_type
{
};

T{}.Draw()가 있으면 true_type으로 분기 그렇지 않다면 false_type쪽으로

std::false_type, true_type은 내부적으로
value를 갖게 한다.

template<typename T>
std::enable_if_t<
    HasDraw<T>::value
>
Render(T& obj)
{
    obj.Draw();
}
```

concept
```cpp
template<typename T>
concept Drawable =
requires(T t)
{
    t.Draw();
};
```

만약 부정 조건일 때를 사용하고 싶은경우
```cpp
template<typename T>
requires (!Drawable<T>)
void Render(T& obj)

혹은 

template<typename T>
void Render(T& obj)
{
    if constexpr (Drawable<T>)
    {
        obj.Draw();
    }
    else
    {
        std::cout << "Draw not supported\n";
    }
}
로 내부에서 분기 처리 구현
```
\
\
\
심화: 과거 분기 처리 구현
```cpp
template<typename T>
void RenderImpl(
    T& obj,
    std::true_type)
{
    obj.Draw();
}
template<typename T>
void RenderImpl(
    T&,
    std::false_type)
{
    std::cout << "No Draw\n";
}

template<typename T>
void Render(T& obj)
{
    RenderImpl(
        obj,
        HasDraw<T>{}
    );
}

HasDraw<T>{} 이것은 std::true_type{} 또는 std::false_type{} 객체가 된다.
```