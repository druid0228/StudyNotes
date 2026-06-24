# GameObject / Component

### Inheritance Hierarchy
System.Object -> UnityEngine.Object -> UnityEngine.GameObject

System.Object -> UnityEngine.Object -> UnityEngine.Component

---

유니티의 기본철학\
유니티는 GameObject + Component로 모든 것들을 만든다.

컴포넌트 컨테이너. GameObject 자체는 생각보다 비어있다.


```c#
GameObejct player = new GameObject();
만들면 실제로는

Player
 └ Transform

만 존재
```

가장 많이 보는 함수
GetComponent<T>();
gameObject.AddComponent<T>();

참조 : Transform은 GameObject의 필수 컴포넌트 Destroy 불가능함\
같은 GameObject에 컴포넌트들은 편의를 위해 transform.position등\
단순 접근이 가능함 내부적으로 gameObject.GetComponent<Transform>()를 제공
