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

### 23. Auto Activated Abilities

커스텀 ASC에서 OnGiveAbility를 override하여 기능을 추가했다.
CCTags::CCAbilities::ActivateOnGiven 라는 새로운 태그를 추가했다.

HandleAutoActivateAbility(const FGameplayAbilitySpec& AbilitySpec)
FGameAbilitySpec의 AbilitySpec.Ability->GetAssetTags()로 태그를 얻고 tag.MatchesTagExact로
AbilitySpec.Ability의 AssetTags에 해당 태그가 있다면 TryActivateAbility(AbilitySpec.Handle) 한 뒤에 종료하는 함수를 만들었다.

헷갈리지 말 것 작성 코드에서 여러 어빌리티를 보는 것이 아닌 하나의 어빌리티에 대해 태그들을 순회하여 매칭이되면 Activate

OnGiveAbility내부에서 HandleAutoActivateAbility(AbilitySpec) 를 호출했다.
OnGiveAbility는 서버에서 Ability 하나가 given되면 동작하는 형태다.

그래서 클라이언트에서도 동작하기 위한 함수가 필요한데 OnRep_ActivateAbilities()를 사용한다.
이 함수는 ActivatableAbilities 목록이 복제(Rep)되어 갱신되었을 때 불리는 함수고 인자가 없는 것을 보다시피 어떤 것이 복제 되었는지는 알 수 없다
```cpp
void UCC_AbilitySystemComponent::HandleAutoActivateAbility(const FGameplayAbilitySpec& AbilitySpec)
{
	if (!IsValid(AbilitySpec.Ability))return;
	for (const FGameplayTag& tag :AbilitySpec.Ability->GetAssetTags())
	{
		if (tag.MatchesTagExact(CCTags::CCAbilities::ActivateOnGiven))
		{
			TryActivateAbility(AbilitySpec.Handle);
			return;
		}
	}
}

// 서버
void UCC_AbilitySystemComponent::OnGiveAbility(FGameplayAbilitySpec& AbilitySpec)
	HandleAutoActivateAbility(AbilitySpec)

// 클라
void UCC_AbilitySystemComponent::OnRep_ActivateAbilities()
	...
	FScopedAbilityListLock ActiveScopesLock(*this);
	for (auto& AbilitySpec : GetActivatableAbilities())
	{
		HandleAutoActivateAbility(AbilitySpec);
	}

// 두 개 다 UCustomAbilitySystemComponent에 포함되어야함.
```
그러므로 보유한 Abilities들을 순회하면서 HandleAutoActivateAbility 함수를 호출해줘야 한다.
참고 전체 배열을 순회 해야하므로 Lock이 필요하다 현재 블록에 대해 AbilitiyList를 Lock하는 것으로 보임
일반적인 구조같다.

시작 시 한 번 Activate되고, 내부에서 Wait Gameplay Event 같은 Task를 등록해 대기하는 형태의 ability를 위해 이러한 구조를 갖는 것으로 보인다.


### 24. Hit React Ability

CCTag들을 추가했다.\
CCTags.CCAbilities.Enemy.Hitreact\
CCTags.Events.Enemy.Hitreact\
CCAbilities쪽은 에디터에서 Events쪽은 코드에서 추가했다.\
CCAbilites.Primary는 코드에서 추가했었는데 HitReact는 왜 구분했는지 나중에 설계를 봐야 알 수 있을 것 같다.\
예상으로는 CCAbility쪽은 Ability의 식별용 태그이고 Events는 이후 코드에서 호출할 일이 있으니까 나눠둔것으로 추정됨


GA_CC_HitReact를 작성했다. CC_HitReact는 코드고 GA붙은건 BP이므로 구분 유의해야한다.\
태그로 CCTags.CCAbilities.ActivateOnGiven과 CCTags.CCAbilities.Enemy.HitReact를 부여했다.\
해당 GA를 두 enemy class BP_Enemy_Melee와 BP_Enemy_Ranged에 부여해야하는데,\
각각 하는 것이 아니라 두 BP의 공통 Parent인 CC_EnemyCharacter를 기반으로 BP_CC_EnemyBase를 생성하고,
BP_Enemy_Melee와 BP_Enemy_Ranged의 Parent를
BP_CC_EnemyBase로 변경했다.\
이렇게 공통의 부모를 갖는 BP들에 대해 어빌리티등 에디터에서 공통으로 부여해야 하는 것이 있다면,\
BP로 묶어서 할 수 있다는 것을 알 수 있게 되었다.

GA_CC_HitReact에서 Wait Gameplay Event를 사용하여
CCTags.Events.Enemy.HitReact 이벤트를 대기하도록 설정.\
관련된 Event를 기다리도록 셋팅까지 완료했다. 다음 강의를 통해 이제 공격등의 hit 판정시 해당 이벤트를 호출해,\
피격받는 montage를 실행시키면 될 것이다.\

주의 Ability 설정의 Net Execution Policy를 local에서 Server Initiated로 변경.
Instancing Policy는 Instanced Per Actor로

ActivateOnGiven 태그 덕분에 Ability가 GiveAbility될 때
ASC가 자동으로 TryActivateAbility를 호출하여 활성화된다.

참고: BP_CC_EnemyBase는 최대한 비워둔다. cast할 때 불필요한 메시나 에셋 로딩 피해야함. 이 것은 공통으로 어빌리티를 부여하기 위해 만들어진 BP이므로.



### 25. Overlap Test

이번 강의에서는 GA_Primary event graph에서 기존의 Draw Debug Sphere of Hitbox를 BP의 node로 구현한 것에서 C++로 대체 하는 것이 목적이다.

우선 이 프로젝트에서 ObjectType Pawn의 Collision preset에서 Collision Responses의 Visibility를 Block으로 설정한다. 우선 편의를 위해 이렇게 구현한 것으로 보인다.\
보통은 Channel를 따로 파는 것이 정석으로 알고있다. HitRay, InteractRay 등등.

추가: CollisionEnabled의 Query, Physics, Probe 차이\
Query: 직접적인 검출 e.Ray\
Physics: 충돌\
Probe: 충돌 반응 없이 Overlap/Hit 정보만 얻기 위한 감지

Query와 Probe의 차이는
Query는 직접 Trace, Sweep, Overlap 등을 수행하는 형태이고
Probe는 외부의 Query나 충돌이 들어오기를 기다리는 형태로 이해하면 될 것 같다.

이후 Primary Ability만 갖는 특성을 만들기 위해 UCC_GameplayAbility를 상속받는 UCC_Primary class를 작성.\
HitBoxOverlapTest BPCallable를 생성했다.\
내부적으로는 GetWorld()->OverlapMultiByChannel() 호출이 전부지만 인자로 필요한 객체들을 셋팅했음.

많이 쓰이는 형태이므로 코드 첨부
```cpp
	// ACS의 Actor(사용자)를 무시를 추가한다.
	TArray<AActor*> ActorsToIgnore;
	ActorsToIgnore.Add(GetAvatarActorFromActorInfo());	

	// Ensure that the overlap test ignores the Avatar Actor
	FCollisionQueryParams QueryParams;
	QueryParams.AddIgnoredActors(ActorsToIgnore);
	
	// 모든 채널을 ECR_Ignore한 뒤에 Pawn만 Block으로 Overlap은 Collision ObjectType Pawn에만
	FCollisionResponseParams ResponseParams;
	ResponseParams.CollisionResponse.SetAllChannels(ECR_Ignore);
	ResponseParams.CollisionResponse.SetResponse(ECC_Pawn,ECR_Block);
	
	TArray<FOverlapResult> OverlapResults;
	FCollisionShape Sphere = FCollisionShape::MakeSphere(HitBoxRadius);
	
	const FVector Forward = GetAvatarActorFromActorInfo()->GetActorForwardVector()*HitBoxForwardOffset;
	const FVector HitBoxLocation = GetAvatarActorFromActorInfo()->GetActorLocation() + Forward + FVector(0,0,HitBoxElevationOffset);
	
	GetWorld()->OverlapMultiByChannel(OverlapResults,HitBoxLocation,FQuat::Identity,ECC_Visibility,Sphere,QueryParams,ResponseParams);
```
OverlapMultiByChannel은 ECC_Visibility 채널 기준으로 Overlap 검사를 하되,
ResponseParams에서 Pawn만 Block으로 설정했기 때문에 Pawn ObjectType만 결과에 잡히도록 만든다.

한번에 작성하는 것을 보여줬지만 실제로는 GetWorld()->OverlapMultiByChannel를 먼저 작성한 뒤 인자를 채워넣는 식으로 해도 문제 없다.

참고: ObjectType Pawn이지만 ECC_Visibility는 ignore인 객체도 존재 할 수 있다. Channel과 ObjectType을 나누고 조합해서 생각해야한다.

주의: 강의에서 영상에서는 GA_Primary의 Parent Class를 CC_Primary로 바꾸었는데,  Draw Debug Sphere of Hitbox 부분을 지우지 않고 이러한 동작을 하면 해당 노드 안에 HitBoxRadius 라는 변수가 CC_Primary.h에 똑같은 이름으로 EditDefaultsOnly속성으로 존재하고 있어서\
CC_Primary.HitBoxRadius is not blueprint visible (BlueprintReadOnly or BlueprintReadWrite).와 같은 오류가 나왔었다.

### 26. Hit React Gameplay Event


OverlapResult로 찾아낸 Actor들에게 Event를 보내는 것을 만들었다.\
UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* FGameplayTag, FGameplayEventData payload) 로 단순한 형태의 함수로 간단히 보낼 수 있다.


BP에서 쓰기 위해 함수를 고치거나 분리했다.\

```cpp

	// return void에서 HitResult들을 Return 하도록 변경
	UFUNCTION(BlueprintCallable,Category="Crash|Abilities")
	TArray<AActor*> HitBoxOverlapTest();
	
	// Send함수 부분을 묶어서 BluprintCallable로
	UFUNCTION(BlueprintCallable,Category="Crash|Abilities")
	void SendHitReactEventToActor(const TArray<AActor*>& ActorsHit);
	{
		// 내용이 짧고 사용 느낌을 보이기 위해 함수 정의 첨부.
		for (AActor* HitActor : ActorsHit)
		{
			FGameplayEventData Payload;
			Payload.Instigator = GetAvatarActorFromActorInfo();
			UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(HitActor,CCTags::Events::Enemy::HitReact,Payload);
		}
	}
	
	
	// DrawDebugSphere 부분도 함수로 분리
	void DrawHitBoxOverlapDebugs(const TArray<FOverlapResult>& OverlapResults,const FVector& HitBoxLocation)const ;
```

강의에서 처음에는 HitResult를 loop 돌면서 SendGameplayEventToActor 하는 방식에서 이런 느낌으로 변경하였다.

이제 GA_CC_Primary에서 Wait Game Play Event에 Tag::Primary로 received 되면,\
cpp로 작성한 해당 함수들을 node로 Hit Box Overlap Test : return -> SendHitReactEventToActor. fnode로 연결하여 사용했다.

그리고 확인하기 위해 GA_CC_HitReact에서 Tag::HitReact Received 되었을때 print string으로 확인했는데, 이 때 Append(string) 을 이용하여\
payload의 Break Gameplay Event Data의 Instigator->(Get Display Name)->Append.B로 넣어 어떤 액터로부터 이벤트가 received 되었는지 확인하도록 하였다.

Payload.Instigator = GetAvatarActorFromActorInfo();를 설정했으므로 payload 항목에서 확인 가능한 것이다.


### 27. Calculating Hit Direction


우선 bDraw Debugs 분기 Print String 하던 것을 UCC_GameplayAbility 내부로 옮겨줬다.\
ActivateAbility override 했고.\
GEngine->AddOnScreenDebugMessage 로 작성\
GEngine은 엔진의 전역 객체이다.

노드들을 선택해서 Collapse to Function을 사용하면 함수노드로 묶어서 정리할 수 있다.


GetHitDirection을 작성 할 것인데 여러 곳에서 재사용할 수 있으니 static 함수로 만들고, UBlueprintFunctionLibrary를 상속받아 작성했다.

UBlueprintFunctionLibrary를 상속받고 UFUNCTION을 사용하면
프로젝트 전역에서 Blueprint 노드로 사용할 수 있다.

```cpp
UENUM(BlueprintType)
enum class EHitDirection : uint8
{
	Left,Right,Forward,Back
};

UCLASS()
class CRASHCOURSE_API UCC_BlueprintLibrary : public UBlueprintFunctionLibrary
{
	GENERATED_BODY()
	
public:
	
	UFUNCTION(BlueprintPure)
	static EHitDirection GetHitDirection(const FVector& TargetForward, const FVector& ToInstigator);
	
	UFUNCTION(BlueprintPure)
	static FName GetHitDirectionName(const EHitDirection& Direction);
};
```

```cpp
	BlueprintPure
		= 계산 전용 함수
		= 상태 변경 X
		= Exec 핀 없음

	BlueprintCallable
		= 실행 함수
		= 상태 변경 가능
		= Exec 핀 있음
```

방향 판정은 Dot과 Cross로 구현했다.
TargetForward와 Target에서 Instigator로 향하는 방향 벡터(ToInstigator)를 이용해 계산한다.

참고:
Instigator의 Forward 방향이 필요한 것이 아니라,
Target 기준에서 Instigator가 어느 방향에 있는지를 나타내는 벡터가 필요하다.

