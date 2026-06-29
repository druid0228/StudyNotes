# Format

## C++20


std::format 타입 안전한 문자열 포맷 함수.

```cpp
std::string S =
    std::format(
        "{} {} {}",
        Name,
        Level,
        HP);

std::format(
    "{1} {0}",
    "World",
    "Hello");       
```

정렬, 자리수, 진법, 부동소수점 출력 등 다양한 포맷 옵션을 지원한다.

