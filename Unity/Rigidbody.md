# Rigidbody

GameObject가 Unity 물리 시스템의 영향을 받도록 하는 Component

- 중력
- 충돌 반응
- 질량
- 속도
- 힘
- 회전 관성


### 기본구조
```
GameObject
├ Transform
├ Collider
└ Rigidbody
```
- `Collider`는 충돌 범위를 정의한다.
- `Rigidbody`는 물리적으로 움직이고 반응하게 한다.

### AddForce

힘을 주는 방법
```C#
private Rigidbody rigidbody;

private void FixedUpdate()
{
    rigidbody.AddForce(Vector3.forward * 10f);
}
```


### MovePosition
Rigidbody를 사용하는 상태에서 목표 위치로 이동할 때 사용한다.
```C#
private void FixedUpdate()
{
    Vector3 nextPosition =
        rigidbody.position + moveDirection * speed * Time.fixedDeltaTime;

    rigidbody.MovePosition(nextPosition);
}
```
- 물리 기반 캐릭터 이동
- 움직이는 발판
- 충돌을 고려한 일정한 이동


### Is Kinematic
`Is Kinematic`을 켜면 Rigidbody가 물리적인 힘에 의해 움직이지 않는다.
```
Is Kinematic = false: 중력과 힘의 영향을 받음

Is Kinematic = true: 코드나 애니메이션으로 직접 제어
```

### Use Gravity
Unity 중력의 영향을 받을지 안받을지

### Constraints
특정 위치축이나 회전축을 고정한다.

예
```
Freeze Position Y
Freeze Rotation X
```


