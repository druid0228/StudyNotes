# apply

## C++17

tuple, pair, array등에 들어있는 값을 함수 인자로 한 번에 펼쳐주는 기능

기존의 문제
```cpp
void Print(int a, float b, std::string c)
{

}

std::tuple<int,float,std::string> Data = {
    10,3.14,"hello"
}


Print(Data) // ❌
```
인자를 하나하나 넣어서 호출했어야했다.\
Print(get<0>,get<1>,...)


`std::apply`
```cpp
std::apply(Print,Data);
```
로 내부적으로 tuple의 각 원소를 함수의 인자로 전달하여 호출한다.
