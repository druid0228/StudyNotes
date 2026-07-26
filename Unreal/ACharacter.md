# ACharacter

### Inheritance Hierarchy
UObject -> AActor -> APawn -> ACharacter

---

`ACharacter`는 사람형 캐릭터를 위해 만들어진 Pawn이다.

APawn과 차이

- 걷기
- 뛰기
- 점프
- 중력
- 계단 오르기
- 네트워크 이동 복제

등을 기본으로 제공한다.

기본으로 포함하는 Component
```
ACharacter
│
├── CapsuleComponent (Root)
├── CharacterMovementComponent
└── SkeletalMeshComponent (Mesh)
```

사용하지 않는 경우

- 자동차
- 드론
- 비행기
- 카메라 Pawn
- RTS 유닛


자주 사용하는 함수
```cpp
GetMesh();
GetCharacterMovement();
```