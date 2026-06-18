# Unreal Engine 5 Gameplay Ability System(GAS) Crash Course

## Section 1: Introduction
...

## Section 2: Ability System Component
...


## Section 3: Gameplay Events
...

### 19. Scetion 3 Intro

### 20. Gameplay Events

Anim Notify를 상속받아 BP를 생성한다.(AN_SendEventToActor)
UE animation은 특정 시점에 Event notify를 보낼수 있게 되어있다. 강의에서는 Montage에서 공격이 닿는 시점에 보내게 해뒀다.
AN_SendEventToActor에서 Received Notify를 override 하여 직접 구현한다. 
목적은 Send Gameplay Event to Actor를 사용하는 것이다.

Mesh Comp의 Get Owner를 하면 Character가 얻어지고 Get Ability System Component 함수로 ASC를 얻는다
ASC의 Get Owner를 해서 PlayerState를 얻어 AN_SendEventToActor BP에서 Send Gameplay Event to Actor의 Actor 인자로 넣어준다.

참고:
Get Owner를 여러 번 사용하는 이유는 Gameplay Event를 전달할 ASC Owner(PlayerState)를 찾기 위함이다.
현재 프로젝트에서는 PlayerState만 IAbilitySystemInterface를 구현하므로,
Send Gameplay Event to Actor의 Actor 인자로 PlayerState를 전달해야 한다.

또한 Event Tag 인자 혹은 Payload는 BP를 여러개 만들어서 쓰지 않기 위해 변수화 시켜서 사용한다.
Montage에서 Notify를 배치할 때 Event Tag를 설정한다.
Gameplay Ability의 Wait Gameplay Event는 동일한 Tag를 대기해야 한다.

예:CCTags.Events.Player.Primary  또한 이 tag들은 blueprint 상에서만 사용할 것이므로 code의 tag에서 등록하지 않아도 된다.

마지막으로 Gameplay Ability BP에서 sequence로 기존 PlayMontageAndWait을 seq0에 넣고
Wait Gameplay Event를 seq1에 넣으면
Animation의 특정 시점에 Notify가 Gameplay Event를 전송하면 ASC에 전달된다.
ASC 내부에서 해당 Event Tag를 기다리는 AbilityTask(Wait Gameplay Event)가 반응한다.

GA_Primary에서는 CCTags.Events.Player.Primary Tag를 Wait Gameplay Event로 대기 중이므로
Event Received가 호출되고 이후 그래프에서 원하는 동작을 수행할 수 있다.
현재는 Debug Sphere를 그리고 있다.

참고 Sequence는 순차 실행되지만 두 노드 모두 Latent Task 이므로 재생과 대기가 동시에 진행된다.

주의 Event Received 말고 위쪽 Exec에 연결하면 안됨 이곳은 바로 실행되는 곳임.

### 21. Enemy Class

EnemyCharacter class를 base로부터 생성한다.
Enemy의 경우 ASC를 Character 자신이 소유한다.
(Owner Actor = Avatar Actor = Enemy Character)
생성자에서 CreateSuboject를 하고 Set Replicate 설정을 한다.
그 이후 BeginPlay에서 InitAbitityActorInfo등 추가 설정을한다.

Ability 부여는 서버 권한에서만 수행해야 하므로
GiveStartupAbilities는 HasAuthority() 체크 후 호출한다.


Enemy 의 ABP_Enemy_Ranged에서
GetPawn을 Cast Chracter해서 Get Movement Component 거기서 Velocity 얻고 Vector Length.XY로 얻는 value를 speed로 사용했다.

그렇지만 특이한 것은 더 좋은 방법으로 Event Blueprint Initialize Animation에서 Cast한 Chracter 자체를 Value로 Owning Chracter로 저장 한 뒤에

Blueprint Thread Safe Update Animation을 override 해서 Property Access 라는 것을 사용 아까 velocity를 얻었던 경로를 노드 하나로 접근해서 speed를 set 하는 방식으로 바꾸었다.
별개의 스레드로 돌아간다는데 추가 공부 필요

확인: Blueprint Update Animation와 Blueprint Thread Safe Update Animation의 차이는
실행 thread가 다르다 Game Thread vs Worker Thread (병렬 애니메이션 스레드)
대신에 이 함수에서는 Actor, ChracterMovement등에 접근이 제한된다.
게임 스레드에서 어떠한 동작을 했을 때 문제가 생길 수 있기 때문에.

Property Access node는 Thread safe하게 동작한다. UE에서 권장하는 animation 방식이다.

IdleToRun Blendspace를 배치하고 그 뒤에 Montage\Slot "DefaultSlot"을 두었다 그 뒤에가 Output Pose

Montage가 발생하면 slot을 찾아 그 slot에서 실행 하는 것 같다.

Idle,Walk,Run등은 State Machine으로 관리하고
이벤트성 애니메이션들은 Montage로 이런식으로 앞의 anim을 무시하고
바로 재생되도록 하는것이 기본 형태로 보인다.

참고: Enemy Range와 Melee를 만들때 후자는 앞의 것을 복사했는데
Event graph에 variable이 없어서 컴파일 문제 생긴다
노드 우클릭하면 해당 variable 바로 생성 가능하다.

각 Skeletal에 맞는 Animation을 선택하여 만들것.

이번 강의로 간단한 적 캐릭터 BP를 생성하고 Idle animation을 넣고
hit react montage의 준비를 하는 과정이 있었다.

참고:
```cpp
void ACC_EnemyCharacter::BeginPlay()
    ...
	if (!HasAuthority())return;
	
	GiveStartupAbilities();
```
GiveStartupAbilities는 서버만 실행한다. 서버와 클라이언트를 분리하여 코드를 따로 작성하지 않지만 HasAuthority로 서버면 아래 함수들을 실행하고 클라이언트면 실행 안하도록 분기한다.

### 22. Custom Ability System Component

UAbilitySystemComponent를 상속받아 UCC_AbilitySystemComponent class를 생성하고
PlayerState와 EnemyChracter의 CreatDefaultSubobject<UAbilitySystemComponent> 부분만 교체해주었다.
커스텀 ASC를 사용하여 프로젝트에 필요한 기능을 추가하려는 것으로 보인다
