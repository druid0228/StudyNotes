# UScene Component

### Inheritance Hierarchy
UObject -> UActorComponent -> USceneComponent

---

USceneComponent에 더해
충돌과 렌더링 물리 등을 포함한 Component

대표적인 자식들
```
UPrimitiveComponent
│
├ UStaticMeshComponent
├ USkeletalMeshComponent
├ UShapeComponent
│   ├ UBoxComponent
│   ├ USphereComponent
│   └ UCapsuleComponent
│
├ UInstancedStaticMeshComponent
└ ...
```
Mesh나 Shape이 포함되어 있는 것을 볼 수 있음

```cpp
    Mesh->SetCollisionEnabled(ECollisionEnabled::Type)
    Mesh->AddForce(...);
    GetComponentBounds(...)

    BoxComponent->OnComponentBeginOverlap.AddDynamic(
    this,
    &AMyActor::OnOverlapBegin);
```
이런 함수들을 지원

충돌 정보를 보유하고 있다.
Bounds 를 갖고있다.

기본 도형 즉 최소 형태의 구분으로 Primitive라는 단어를 사용 한 것 같다.
primitive_topology나 primitive shader와 잘 구분해서 생각할것.

USceneComponent는 Transform만 가진다.
UPrimitiveComponent는 Transform에 더해 렌더링/충돌 정보를 가진다.

특이한 것 형태와 충돌을 같은 계층에 묶은 것은 언리얼의 설계인가 보다
Scene 과 Primitive사이에 형태는 있지만 충돌하지 않는 계층이 있을법도 하지만
언리얼은 그렇지 않음. 그러므로 언리얼에서는 형태가 있으면 충돌도 있는 것으로 여긴다고 생각 할 수 있겠다.

물론 collision은 설정으로 비활성화 가능하다.