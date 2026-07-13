# UFUNCTION

멤버 함수를 Unreal Reflection System에 등록하는 매크로
```cpp
UFUNCTION()
void func();
```

엔진이 Reflection을 통해 Blueprint, Network 등의 시스템에서 사용할 수 있게 된다.

## Specifier

### BlueprintCallable

Blueprint에서 호출 할 수 있다.
```cpp
UFUNCTION(BlueprintCallable)
void Heal();
```

### BlueprintPure

Blueprint Exec 핀이 없다.\
상태를 변경하지 않고 값만 반환하는 함수에 사용한다.
```cpp
UFUNCTION(BlueprintPure)
float GetHealth() const;
```

### BlueprintImplementableEvent

C++에서 구현하지 않고 Blueprint가 구현한다.\
예: 피격효과, UI이벤트, 사운드재생등
```cpp
UFUNCTION(BlueprintImplementableEvent)
void PlayHitEffect();
```

### BlueprintNativeEvent

C++에서 기본 구현이 가능하고\
Blueprint Override도 가능하다.
```cpp
UFUNCTION(BlueprintNativeEvent)
void Attack();
```

C++에서 실제로는
```cpp
void Attack_Implementation();
```
XXX_Implementation 을 구현한다.


## Category

Blueprint에서 카테고리를 지정한다.
```cpp
UFUNCTION(BlueprintCallable, Category="Skill")
```

## RPC (Remote Procedure Call)

멀티플레이 관련

### Server
서버에서 실행된다.
```cpp
UFUNCTION(Server, Reliable)
void ServerReload();
```

### Client
클라이언트에서 실행된다.
```cpp
UFUNCTION(Client, Reliable)
void ClientShowDamage();
```

### NetMulticast
서버에서 호출하면 서버와 모든 클라이언트에서 실행된다.
```cpp
UFUNCTION(NetMulticast, Reliable)
void MulticastExplosion();
```

### Reliable / Unreliable

Reliable: 반드시 전달.
```cpp
UFUNCTION(Server, Reliable)
```

Unreliable: 유실 될 수 있다.\
자주 보내는 데이터. 예: 총알 흔적, 발자국
```cpp
UFUNCTION(Server, Unreliable)
```

### Exec
콘솔 명령으로 호출 가능
```cpp
UFUNCTION(Exec)
void KillAll();
```