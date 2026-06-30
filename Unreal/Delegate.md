# Delegate


함수를 저장하고 나중에 호출할 수 있는 객체.\
대행자\

객체끼리 직접 참조하지 않고 이벤트를 전달하여 결합도를 낮출 수 있다.

```cpp
DECLARE_DELEGATE()
DECLARE_DELEGATE_OneParam()
DECLARE_MULTICAST_DELEGATE()
```


예시
```cpp
선언
DECLARE_DELEGATE(FOnDead)

멤버
FOnDead OnDead

등록
OnDead.BindUObject(this,&UMyWidget::UpdateUI)

호출
OnDead.Execute()

그러면 UpdateUI() 실행
```

추가 : 일종의 타입 만들기
```cpp
개념적 예시
DECLARE_DELEGATE(FOnDead);
using FOnDead = TDelegate<void()>;
```


### Broadcast

Multicast Delegate는 여러 객체를 등록할 수 있으며,\
호출하는 쪽은 어떤 객체가 등록되어 있는지 알 필요가 없다.\
Broadcast()를 호출하면 등록된 모든 객체에게 전달된다.\
예를 들어 HP가 변경되면, UI, 사운드, 이펙트 등 필요한 객체들에게 동시에 알릴 수 있다.

```cpp
DECLARE_MULTICAST_DELEGATE(FOnDead);

OnDead.AddUObject(...);

OnDead.AddUObject(...);

OnDead.AddUObject(...);

OnDead.Broadcast()
```


### Dynamic Delegate
BP용
```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE(...)
```
실제로는 DECLARE_DELEGATE 매크로가 내부적으로 이러한 타입을 생성해 준다.

일반적으로 쓰는 AddDynamic\
Dynamic Multicast Delegate


Tick과 차이: 매 순간 호출 되고 검사하는 것이 아니라\
실제 데이터의 변경이 일어날 때만 호출 가능하다.\
Observer Pattern

## 대표 종류 3가지

### Delegate

단일 delegate. 하나의 함수만 등록.\
대표적 사용: 콜백, 비동기, Timer, Task.\
Delegate.Execute()

### Multicast Delegate

여러개 등록 가능한 delegate.\
Multicast.Broadcast()로 전부 호출\
대표적 사용: 대표적 사용: HP 변경, 데미지 처리, 레벨업 알림.


### Dynamic Multicast Delegate

BP에서도 Assign, Bind Event 가능\
Reflection과 Blueprint를 지원하는 대신 일반 Delegate보다 약간의 오버헤드가 있다.\
DynamicMulticast.Broadcast()\
대표적 사용: 위젯, 버튼, BP event


## Bind

Bind 계열 함수들
```cpp
BindRaw()
BindLambda()
BindStatic()
BindUObject()

AddUObject()
AddDynamic()
```

Bind vs Add\
single이냐 multicast냐\
Bind는 기존 바인딩을 교체한다.\
Add는 새로운것도 구독하는 형태


### BindUObject
예
```cpp
BindUObject(
    this,
    &UMyWidget::UpdateHP
);
```
UObject 멤버 함수 등록의 의미.\
이유는 UObject는 언리얼 GC가 UObject 파괴했을 때\
UObject가 GC로 파괴되면 Delegate도 이를 인식하여 안전하게 처리할 수 있다.

### BindRaw
단순 C++객체
```cpp
class MyClass
{
};
```
만약 객체가 죽었는데 Delegate 호출하면 크래시 날 수 있음 조심.

### BindStatic
정적함수
```cpp
static void Print();
```
함수를 등록하는 것이다 객체 없음

### BindLambda
```cpp
BindLambda(
[]
{
    UE_LOG(...);
});
```
간단한 처리용

### AddUObject
DECLARE_MULTICAST_DELEGATE용\
예
```cpp
OnChanged.AddUObject(
    this,
    &UMyWidget::Update
);
```

### AddDynamic

DECLARE_DYNAMIC_MULTICAST_DELEGATE용.
Blueprint에서 Bind Event / Assign이 가능하다.