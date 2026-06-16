# UScene Component

### Inheritance Hierarchy
UObject -> UActorComponent -> USceneComponent

---

SceneComponent는 Transform을 갖고 있는 Component이다.

Actor는 RootComponent로 SceneComponent를 소유하여 Transform을 갖는다.
Actor는 월드에 존재하지만 SceneComponent없이 Transform을 보유하지 않는다.

주요 기능 Attachment
SetupAttachment(comp) 함수를 사용하여 부모를 설정하고
부모가 transform하면 자식한테도 전파된다.

예
```
RootComponent
    └ CameraBoom
        └ FollowCamera
```
