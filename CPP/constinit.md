# Constinit

## C++20

컴파일 타임에 결정 가능한 값으로 초기화되어야 한다.\
동적 초기화를 허용하지 않는다.

문제:\
컴파일 타임에 초기화될 수도 있고 프로그램 시작시에 초기화 될 수도 있다.\
Static Initialization Order Fiasco 문제.

```cpp
.h
extern int GlobalValue
extern int Other

A.cpp
int GetValue(){return 10;}
int GlobalValue = GetValue();

B.cpp
int Other = GlobalValue;
```

GlovalVaule가 먼저일지 Other가 먼저일지 알 수 없음

```cpp
constinit int Value = 10
```
반드시 컴파일 타임 초기화를 해라.

주로 전역변수, 정적변수, 싱글톤 상태 등에 사용

추가 예시
```cpp
constinit int Value = 10; // OK

int GetValue();

constinit int Value2 = GetValue(); // Error
```
