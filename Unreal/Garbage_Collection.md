# Garbage Collection (GC)

언리얼은 `UObject`를 GC로 관리한다.


## GC는 무엇을 기준으로 제거할까?
더 이상 도달할 수 없는 UObject는 엔진이 자동으로 제거한다.

참조(Reference)가 남아있는 것을 기준으로 확인하여 제거한다.


## Root Set
Root Set 부터 탐색을 시작한다.\
대표적으로\
- UWorld
- GameInstance
- Loaded Asset
등이 Root Set에 포함된다.


## Reachability

Root부터 참조를 따라간다 이를 Reachability 라고 한다.\
루트에서 도달 가능한 객체는 GC되지 않는다

## GC 과정

```
Root Set
↓
Reference 탐색
↓
도달 가능한 UObject
↓
유지
↓
도달 불가능한 UObject
↓
GC 대상
```

---

일반적으로 UPROPERTY가 있어야 GC가 참조를 알 수 있다.

## Destroy와 GC

Actor에서
```cpp
Destroy()
```
를 호출해도\
즉시 메모리에서 삭제되지 않는다.

## Outer

```cpp
NewObject<UMyObject>(Outer);
```
Outer는
- 객체의 소유 관계
- 계층 구조
- 이름 생성
등을 위한 것이다.

GC 생존 여부는 Reachability가 결정한다.


## AddToRoot
```cpp
Obj->AddToRoot();
```
Root Set에 직접 넣는다 -> GC가 삭제하지 않는다

반대로
```cpp
RemoveFromRoot();
```
RemoveFromRoot()를 호출하면 Root Set에서 제거된다.\
이후 Reachability 검사에서 도달할 수 없으면 GC 대상이 된다.