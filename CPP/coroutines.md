# Coroutines

## C++20


함수를 중간에 멈췄다가 나중에 이어서 실행하는 기능


keyword
```
co_await
co_yield
co_return
```

co_await Something()\
기다리고 조건이 되면 이어서 실행

co_yield Value\
값 하나 반환 - 잠시 멈춤 - 다음 호출 시 이어서 실행

co_return\
coroutine 종료


co_await 뒤에 오는건 Awaitable 객체\

다음의 규칙을 만족해야함
```
await_ready()
await_suspend()
await_resume()
```

이해를 돕기위한 대략 구조
```cpp
co_await Task

    ==

if (!Task.await_ready())
{
    Task.await_suspend(...);
}
Task.await_resume();
```

중요 : await_suspend가 즉시 coroutine을 실행시키는 것이 아님\
나중에 Resume 해달라는 것을 등록만 한다.

coroutine은 컴파일러가 timer, sleep, ... 기타 등등의 개념을 모른다.\
컴파일러는 함수호출만 하고 Task가 구현한다.\

co_await은 sleep이나 timer를 직접 제공하는 기능이 아니다.
Awaitable 객체에게 대기/재개 방법을 위임하는 문법이다.


### promise_type

Task는 사용자 정의 반환 타입이다. 컴파일러는 promise_type을 통해 이 타입을 어떻게 만들지 판단한다.

간단 예
```cpp
struct Task
{
    struct promise_type
    {
        ...
    };
};

=> Task::promise_type Promise;
```

promise_type이 실제 coroutine에 사용할 Task를 만들도록 컴파일러를 도와준다고 보면 된다.\


Promise 안에는 반드시 이런 함수가 있다.\
Promise.get_return_object()

promise가 하는일
```cpp
struct promise_type
{
    get_return_object();        // Task 생성

    initial_suspend();          // 처음 실행 여부

    final_suspend();            // 끝났을 때 처리

    return_value();             // co_return 값 처리

    unhandled_exception();      // 예외 처리
};
```

resume()은 누가 호출하는가?\
await_suspend()가 등록한 주체가 한다.\

OS Timer, IOCP Callback, GameEngine, ...

스스로 다시 실행되지 않는다. 외부의 누군가가 resume()을 호출해야한다.


### Coroutine Handle

coroutine을 조종하는 handle. Coroutine Frame을 가리키는 객체.

```
Coroutine Frame
 ┌──────────────────┐
 │ State            │
 │ Local Variables  │
 │ Promise          │
 └──────────────────┘
          ▲
          │
coroutine_handle
```


### suspend_never, suspend_always

즉시실행한다, 항상 suspend한다.\
std::suspend_never는 suspend하지 않고 계속 실행한다.\
std::suspend_always는 항상 suspend한다.
```cpp
initial_suspend()
{
    return std::suspend_never{};
}

final_suspend()
{
    return std::suspend_always{};
}

```



### end

coroutine을 사용하려면 Task, awaitable을 만들어야한다.

사용\
```cpp
Task Foo()
{
    co_await MyAwaiter{};
    co_return;
}
```

1. Task : Struct/Class를 만들고, 그 안에 promise_type을 중첩 타입으로 제공해야한다.\
상속이나 인터페이스가 아닌 이름규칙 기반.\
```cpp
struct Task
{
    struct promise_type
    {
        Task get_return_object()
            return Task{};

        std::suspend_never initial_suspend()    // 반환형이 옵션의 역할을 한다.
            return {};

        std::suspend_always final_suspend() noexcept
            return {};

        void return_value(T value)    // 값을 반환하면 return_value 그렇지 않다면 return_void
            return value

        void return_void()
            return

        void unhandled_exception()
            std::terminate();
    };
};

4가지는 필수
    get_return_object()
    initial_suspend()
    final_suspend()
    unhandled_exception()

```

2. awaitable : 별도 객체에 3가지 함수를 구현해줘야 한다.
```cpp
struct MyAwaiter
{
    bool await_ready()
    {
        return false;
    }

    void await_suspend(std::coroutine_handle<> handle)
    {
        // 나중에 handle.resume() 해줘야 함
    }

    void await_resume()
    {
    }
};
```

보통은 라이브러리를 쓴다.\
boost::asio::awaitable<>
