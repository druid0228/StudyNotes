# shared_ptr

## C++11

하나의 객체를 여러 곳에서 공동 소유할때 사용하는 스마트 포인터.

```cpp
Player* P = new Player();

SystemA->Player = P;
SystemB->Player = P;
SystemC->Player = P;
```
여러 곳에서 같은 객체를 갖고 있을 때, 누가 `delete`를 해야하나?

```cpp
#include <memory>

std::shared_ptr<Player> P =
    std::make_shared<Player>();
```
객체의 공동 소유권을 관리

P.reset()해도 객체는 살아있다.\
마지막 소유가 사라지는 순간 메모리 해제

Reference Count를 관리한다.

복사 : 공동 소유자가 추가되어 Reference Count 증가\
이동 : 소유권만 이전되며 Reference Count는 증가하지 않음

함수들
```cpp
std::make_shared<T>()
get()   // Raw pointer 변환
reset() // 소유권 포기
use_count()
```

주의: get()으로 얻은 raw pointer는 delete해서는 안된다.

---

추가 문제: 상호 소유 일때 서로 카운트가 0이 될수 없어서 메모리 누수가 발생\
Circular Reference / Reference Cycle

예: 
```
Player
  │ shared_ptr
  ▼
Weapon
  │ shared_ptr
  └──────────► Player

Player count = 1  ← Weapon이 소유
Weapon count = 1  ← Player가 소유
```
해결방법 weak_ptr