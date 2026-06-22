# Source_location

## C++20

현재 코드 위치를 얻는 기능\
c++20 이전에 __FILE__ __Line__을 사용했다.\
이것을 잘 묶어둔 형태.\

```cpp
auto Loc = std::source_location::current()

Loc.file_name()
Loc.line()
...

void Log(
 message,
 const std::source_location& loc = std:: source_location::current())

```
Log 함수가 호출된 위치가 찍힌다.

직접 로그를 만들 때는 유용해 보이지만.\
보통 라이브러리 로그를 사용하기 때문에 중요해 보이진 않는다.