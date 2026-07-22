# invoke

## C++17

`std::invoke`는 모든 Callable 가능한 대상을 동일한 문법으로 실행하게 해준다.

Callable 객체(일반 함수, 함수 포인터, 람다, Functor, 멤버 함수 포인터 등)를 동일한 방식으로 호출할 수 있게 해준다.

템플릿에서도 Callable의 종류를 구분하지 않고 동일하게 호출할 수 있다.


Callable은 여러가지가 있다.\
일반함수, 람다, functor, 멤버함수 등\
이 중에서 멤버 함수 포인터는 호출 문법이 다르다.
```cpp
Function()
Lambda()
Functor()

Class.Function()
Class->Function()
```

invoke를 사용하여 통일
```cpp
std::invoke(Function)
std::invoke(Lambda)
std::invoke(Functor)

Class C
std::invoke(&Class::Function, C)
```

템플릿에 사용되는 예시
```cpp
template<typename F, typename... Args>
void Execute(F&& Func, Args&&... Args)
{
    std::invoke(
        std::forward<F>(Func),
        std::forward<Args>(Args)...);
}
```