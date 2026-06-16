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