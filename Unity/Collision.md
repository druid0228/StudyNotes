# Collision
물리적인 충돌

Collision 함수들
```C#
void OnCollisionEnter(Collision collision);
void OnCollisionStay();
void OnCollisionExit();
```

## Collider
충돌은 `Collider`가 담당한다.\
Collider가 충돌을 감지한다.

## Layer Collision Matrix
모든 충돌을 다 검사하면 비효율적이다.
```
Player
Enemy
Bullet
UI
Ground
```
등을 Layer로 구분한다.

## CompareTag
문자열 직접 비교 대신 CompareTag() 사용을 권장한다.
```C#
if (other.CompareTag("Player"))
{
}
```


### Collision / Trigger
* Collision: 실제로 부딪히는 충돌
* Trigger: 겹치는 것만 감지

Collision은 부딪힘 뒤에 물리 반응이 있다.\
Trigger은 겹침 감지 뒤에 물리 반응은 없다.

Collider는 똑같다.\
Is Trigger 옵션으로 결정된다.

```
Is Trigger = OFF => Collision
Is Trigger = ON  => Trigger
```

### Trigger Callback
```C#
OnTriggerEnter()
OnTriggerStay()
OnTriggerExit()
```

### Rigidbody가 필요한 이유
Unity는 Collider , Collider만 있으면\
OnCollision이나 OnTrigger이 항상 호출 되는 것이 아니다.\
일반적으로 두 객체중 최소 하나에는 `Rigidbody`가 있어야 물리 이벤트가 발생한다.