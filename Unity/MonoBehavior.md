# MonoBehavior

### Inheritance Hierarchy
Object -> Component -> Behavior -> MonoBehavior

---

유니티에서 가장 많이 보이는 클래스. 대부분의 로직 스크립트는 MonoBehavior를 상속받음.

엔진 기능을 사용할 수 있음. 리플렉션, 생명주기 함수 등등.

월드에 존재하기 위해서는 GameObject에 붙어야함.

직접 생성(new)하지 않는다.
보통 AddComponent<T>() 또는 Prefab을 Instantiate()하여 사용한다.

Behavior는 enable을 갖고 있음.

MonoBehaviour의 Mono는 과거 Unity가 사용하던 Mono 런타임(Mono Project)과 관련된 이름으로 알려져 있다.\
예전 구현체 이름을 유지중으로 보면 될 것이다.

Behavior와 차이는 이곳부터 스크립트 기능 추가.\
생명주기 함수, coroutine, invoke, trigger
