# ScriptableObject

씬에 존재하지 않는 데이터 Asset

하나의 데이터로 여러 객체가 참조하여 사용 가능하다.

데이터를 공유하기 위해 존재한다.

중요: 모두 같은 ScriptableObject를 참조하므로 런타임에 값을 변경하면\
그 ScriptableObject를 참조하는 모든 객체가 변경된 값을 보게 된다.\
공유되는 기본 설정값을 넣는 것이 일반적이다.


### Prefab과 차이
Prefab\
GameObject 저장\
실제로 씬에 생성 될 것\
예: 플레이어, 적, 총알

ScriptableObject\
데이터만 저장\
설정 데이터\
예: 아이템 정보, 몬스터 스탯, 스킬 데이터