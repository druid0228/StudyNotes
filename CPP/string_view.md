# String_view

## C++17

문자열을 넘길 때 const string& arg 로 받는 형태에서
const char* c 형태로 인자를 넘길 때 암시적으로 std::string 객체를 생성해서 전달했었다.
std::string_view는 불필요한 문자열 생성을 하지 않고 문자열을 다루기 위해 만들어졌다.

```cpp
class string_view
{
    const char* Data;
    size_t Size;
    /*
        class basic_string_view
            const_pointer _Mydata;
            size_type _Mysize;
    */
}
```
대략적인 내부구조가 저렇게 되어 있어서
pointer와 size로 viewer를 구현했다는 것을 알 수 있다.

const char* 과 std::string 등으로부터 생성될 수 있도로 생성자가 작성되어 있다.
이러한 내용들로 함수 인자를 넘겨 줄 때 string container 를 넘겨 주는 것처럼 가능하게 되는 것 같다.

다만 string_view는 읽기 위한 객체에 가깝고 만약 함수 내부에서 string을 수정할 필요가 있다면
string_view를 사용하는 것이 아니라 std::string& 로 넘겨주는 것이 옳다.

size, substr, find 등을 지원하므로 const char* 로 부터 생성된 std::string_view도 std::string처럼
사용 가능한 것도 유용해 보인다.

주의: 포인터가 가리키고 있는 원본 객체가 사라지면 위험해서 문제가 될 수 있다.
string_view가 사용되는 동안에 원본의 문자열의 수명이 보장되어야한다.