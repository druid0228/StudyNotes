# Life Cycle

기본 흐름
```
Awake
↓
OnEnable
↓
Start
↓
Update
↓
OnDisable
↓
OnDestroy
```

### Awake

객체가 생성되면 가정 먼저 호출되는 함수.\
주로 변수 초기화, Component 캐싱을 한다.
```c#
private Rigidbody rb;
void Awake()
{
    rb = GetComponent<Rigidbody>();
}
```

### OnEnable

컴포넌트가 활성화 될 때 호출
```c#
enabled = true;
```
주로 Delegate, Event 등록

### Start

Awake 이후 호출된다.\
Awake는 모든 객체의 Awake가 끝난 뒤 Start가 호출된다.\
게임 시작 준비를 한다.\ 
주로 다른 객체 참조 및 초기 이벤트 등록

```c#
void Start()
{
    enemy = FindObjectOfType<Enemy>();
}
```

### Update

매 프레임 호출\

입력, 이동, 카메라 등 매 프레임 처리가 필요한 것들.

### Fixed Update

고정된 시간 간격으로 호출된다.

기본적으로 약 0.02초(50Hz)마다 실행된다.
FPS와 무관하게 물리 시뮬레이션을 일정한 간격으로 수행하기 위해 사용한다.

예 : 60FPS 1초 60번, 144FPS 1초 144번

주로 Rigidbody 같은 물리 처리

### LateUpdate

Update가 모두 끝난 후 호출\

ex: follow camera(플레이어가 이동 후 카메라 따라가기)

### OnDisable

컴포넌트가 비활성화 될 때 호출

주로 Delegate, Event 해체


### OnDestroy

객체가 없어질 때


추가: OnEnable과 OnDisable이 따로 있는 이유\
`gameObject.SetActive(false)` 처럼 중간중간 enable이 변경되는 경우의 처리를 위해\
일반적인 생성/종료 흐름과는 별도로, 활성 상태 변화에 대응하기 위해 존재한다.