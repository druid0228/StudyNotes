# Modules

## C++20


include를 대체하는 새로운 모듈 시스템.

기존 include의 헤더 소스 복사해서 붙이는 것이 아니라.\
미리 컴파일된 모듈 인터페이스(BMI)를 가져온다.

모듈 정의 export module Math;

공개
```cpp
export int Add(int A,int B)
{
    ...
}

```

import Math


\#pragma once와 차이\
#pragma once는 하나의 번역 단위(Translation Unit)에서 같은 헤더가 여러 번 포함되는 것만 막는다.\
하나의 cpp 파일 단위로 이해해도 된다.

예
```cpp
// A.cpp
#include "Player.h"
#include "Monster.h"    // 내부에서 #include Player.h
```

하지만 만약 cpp가 여러개라면 각각에서 #include "Player.h" 하는 것을 막을 수 없다\
각 cpp(번역 단위)마다 Player.h의 내용을 전처리 단계에서 포함하여 컴파일한다.

기존에는 PCH(Precompiled Header)를 사용하여 자주 사용하는 헤더를 미리 컴파일했다.\
단 한계점 PCH에 포함된 헤더를 사용하지 않는 cpp도 같은 PCH를 사용하므로 불필요한 의존성이 생길 수 있다.\

module의 한계점 기존 C++ 라이브러리들은 전부 include 기반이라 module 지원 전까지는 전환 힘듬.
