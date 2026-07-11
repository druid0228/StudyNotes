# UPROPERTY

멤버 변수를 Unreal Reflection System에 등록하는 매크로다.

엔진이 Editor, Blueprint, GC, Replication, Serialization 등에 사용할 수 있게 된다.

```cpp
UPROPERTY()
int Health; // 엔진이 알 수 있다.

int Health; // 일반 C++ 엔진이 모른다.
```

## Specifier

동작을 결정하는 옵션
```cpp
UPROPERTY(EditAnywhere)
UPROPERTY(VisibleAnywhere)
UPROPERTY(BlueprintReadOnly)
...
```

### EditAnywhere
Editor에서 수정 가능, Blueprint Default에서도 변경 가능

### EditDefaultsOnly
BP의 Class Default만 수정 가능, 레벨에 배치된 Actor 수정 불가.\
모든 인스턴스가 같은 값을 가져야 하는 경우 사용\
예: 최대 체력, 공격력 등...

### EditInstanceOnly
레벨에 배치된 각 Actor마다 수정 가능, BP 기본값 수정 불가.\
예: DoorA 1초, DoorB 5초 .. 액터마다 어떤 동작만 다르게 설정 할 것들.

### VisibleAnywhere
Editor에서 보이지만 수정 불가능

### VisibleDefaultsOnly
BP 기본값에서 보인다 수정은 불가능. 인스턴스에서는 보이지 않는다.\
주로 CreateDefaultSubobject로 만든 Component등에 사용

### VisibleInstanceOnly
BP에서 보이지 않는다. 레벨에 배치된 Actor에서만 보인다.\
사용 빈도 높지 않음, 사용예 currentHP

### BlueprintReadOnly
BP에서 읽기만 가능

### BlueprintReadWrite
BP에서 읽고 쓸 수 있다.


## Category

카테고리를 설정한다 Details Panel에서 표시된다.
```cpp
UPROPERTY(EditAnywhere, Category="Character")
```

## Transient
저장(Serialization)되지 않는다.\
게임을 다시 실행하면 값이 초기화된다.\
예: Runtime Cache, 임시데이터
```cpp
UPROPERTY(Transient)
int32 TempValue;
```

## Replicated
서버의 값을 클라이언트로 복제한다.
```cpp
UPROPERTY(Replicated)
float Health;
```
사용하려면 DOREPLIFETIME(...)도 함께 구현해야 한다.

## ReplicatedUsing
클라이언트에서 값이 복제되어 변경되면 지정한 함수가 호출된다.
```cpp
UPROPERTY(ReplicatedUsing=OnRep_Health)
float Health;
...

OnRep_Health()
```
UI갱신, 효과음 재생 등에 많이 사용한다.

## SaveGame
SaveGame 객체를 저장할 때 같이 저장된다.
```cpp
UPROPERTY(SaveGame)
int32 Gold;
```

## Config
ini파일에서 값을 읽는다.

```cpp
UPROPERTY(Config)
float MouseSensitivity;
```
주로 설정값, 프로젝트 옵션등에 사용


## Meta
Specifier가 아닌 추가 옵션


```cpp
meta=(ClampMin="0", ClampMax="100") // 입력 범위 제한
meta=(UIMin="0", UIMax="100")       // 슬라이더 범위만 제한

private:
UPROPERTY(meta=(AllowPrivateAccess="true")) // private지만 BP에서 접근 가능
float HP;

meta=(DisplayName="Move Speed")     // Editor 표시 이름 변경.
meta=(ToolTip="Character Move Speed") // 마우스 올리면 설명을 표시
```
---

원칙: UObject를 가리키는 멤버 포인터라면 `UPROPERTY`를 붙이는 것이 원칙이다.
```cpp
UPROPERTY()
UStaticMeshComponent* Mesh;

UPROPERTY()
UMaterialInterface* Material;

UPROPERTY()
UTexture2D* Icon;
```


추가: Visible 계열은 포인터 자체를 변경할 수 없다는 의미이다.\
포인터가 가리키는 객체의 속성은 수정 가능하다.

예:
```cpp
UPROPERTY(VisibleAnywhere)
TObjectPtr<UCameraComponent> Camera;
```
Camera 참조를 다른 컴포넌트로 변경할 수 없지만, Detail 패널에서 내부 설정은 수정 가능하다.
