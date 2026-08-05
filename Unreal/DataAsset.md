# Data Asset

### Inheritance Hierarchy
UObject -> UDataAsset

---

데이터만을 저장하기 위한 UObject

코드를 변경하지 않고 데이터만 에디터에서 바꿔 게임을 조정할 수 있다.

구조 예시
```
Weapon Actor
↓
WeaponData
↓
Damage
AttackSpeed
Range
Mesh
Icon
```

Blueprint도 Data를 저장할 수 있지만\
데이터 + 로직이다.

반면 Data Asset은 데이터만 저장되도록 설계

Data Asset은 읽기(Read Only)에 가까운 데이터를 저장한다.

데이터 하나를 여러 객체가 참조 가능하다.\
그래서 런타임에 변하는 상태는 Data Asset에 저장하지 않는 것이 중요하다.

