# jthread

## C++20

std::thread의 안전한 버전.

join을 안했을 경우 스코프 나갈때
```cpp
{
    std::thread T(work)
}   // terminate

{
    std::jthread T(work)
}   // join
```

jthread는 소멸자에서 자동 join\

std::thread는 join() 또는 detach()를 사용자가 직접 선택하도록 설계되었다.\
백그라운드에서 계속 실행(detach)해야 하는 경우도 있으므로\
자동 join이 기본 동작은 아니다.\
그러므로 필요에 따라 thread, jthread 선택해서 사용하면된다.


std::stop_token\
협력적 종료 요청

예시
```cpp
void Work(std::stop_token Token)
{
    int Count = 0;

    while (!Token.stop_requested())
    {
        std::cout << "Working... " << Count << "\n";

        if (Count >= 5)
        {
            std::cout << "Internal condition met. Exit thread.\n";
            break;
        }

        ++Count;
        std::this_thread::sleep_for(std::chrono::milliseconds(500));
    }
}

int main()
{
    std::jthread T(Work);

} // 자동 join
```
