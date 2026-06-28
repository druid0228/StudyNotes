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
