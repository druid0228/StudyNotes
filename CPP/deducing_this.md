# Deducing this

## C++23

deducing(추론)

명시적 매개 변수(Explicit Object Parameter)

기존의 숨겨져 있던 첫 번째 인자 `this`를 매개변수 목록 맨 앞에 직접 드러낸다.

기존에는 this 포인터를 암시적으로 전달 받았기 때문에,\
const 여부와 value category(lvalue/rvalue)에 따라 여러 개의 함수를 오버로딩 해야하는 번거로움이 있었다.
```cpp
void Func()&
void Func()const&
void Func()&&
void Func()const&&
```


예시:
```cpp
class MyString
{
public:

    std::string& Get() &
    {
        return data;
    }

    const std::string& Get() const&
    {
        return data;
    }

    std::string&& Get() &&
    {
        return std::move(data);
    }

private:

    std::string data;
};
```
중복이 너무 많다.

Deducing this 를 사용하면
```cpp
class MyString
{
public:
    template<typename Self>
    decltype(auto) Get(this Self&& self)
    {
        return std::forward<Self>(self).data;
    }
private:

    std::string data;
};
```
하나의 함수 템플릿으로 처리할 수 있다.

explicit object parameter의 타입(Self)을 컴파일러가 자동으로 추론(Deduce)한다.

범용 라이브러리나 Generic 코드를 작성할 때 특히 유용하다.


### 왜 Self&&를 사용하는가
```cpp
this Self&& self
```
이렇게 사용한 이유.
1. 객체를 값으로 복사하지 않고 참조로 전달한다.
2. lvalue와 rvalue를 모두 받을 수 있다. (Self&는 rvalue를 받을 수 없다)
3. 원래의 value category를 유지할 수 있다.(std::forward<Self>와 함께 사용하면 move 지원)

Deducing this에서 가장 일반적으로 사용하는 선언 형태이다.

### std::forward
`std::forward`: 원래 전달받은 값의 성질(lvalue,rvalue)를 그대로 함수로 전달하는 함수

참고:

일반 함수의 Type&& → rvalue reference\
템플릿의 T&& → Forwarding Reference\
두개를 구분해야한다.


사용되는 예시
```cpp
/// LVALUE
MyString lstr{"lvalue"};
std::string& ref = lstr.Get(); // 일반 lvalue

const MyString clstr{"const lvalue"};
const std::string& ref = clstr.Get(); // const lvalue

/// RVALUE
std::string moved = MyString{"Temp"}.Get(); // 일반 rvalue

const std::string moved = MyString{"const Temp"}.Get(); // const rvalue

MyString rstr{"MoveTarget"};
std::string moved = std::move(rstr).Get(); // move도 rvalue 취급

const MyString crstr{"ConstMove"};
const std::string&& ref = std::move(crstr).Get(); // const rvalue
```
lvalue,rvalue 표시는 호출 시 선택되는 Get()의 value category(&, &&, const&, const&&)를 나타낸다.

추가:
```cpp
MyString{"Temp"}.Get();              // -> std::string&&        R
std::move(rstr).Get();               // -> std::string&&        R
lstr.Get();                          // -> std::string&         L
clstr.Get();                         // -> const std::string&   L
std::move(crstr).Get();              // -> const std::string&&  R
```