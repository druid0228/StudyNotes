# APawn

### Inheritance Hierarchy
UObject -> AActor -> APawn

---

`APawn`은 Controller가 조종(Possess)할 수 있는 Actor이다.

플레이어 및 AI

### Possess / UnPossess
Controller는 Pawn을 Possess하여 조종한다.
```cpp
Controller->Possess(Pawn);
```
입력 전달, AI명령, 카메라 제어 등이 가능해진다.

`UnPossess` 조종을 해제 할 수도 있다.\
예: 플레이어 사망, 차량에서 내릴때, 캐릭터의 교체

### Controller 얻기
```cpp
GetController();
```
GetController()는 AController*를 반환한다.\
실제 객체는 `APlayerController` 또는 `AAIController`일 수 있다.

---

추가: APawn 자체는 입력을 자동으로 받지 않는다.\
입력 바인딩이 Controller 또는 Pawn에서 이루어지더라도,\
입력의 소유권은 Controller에 있다.