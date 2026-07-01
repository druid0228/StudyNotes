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

### 28. Hit React Montage



Montage를 Sequence로부터 생성했다..\
특이한 것은 Montage 하나 안에 여러 Sequnce를 넣어서 만든것\
Montage는 관련된 애니메이션들을 하나로 묶어 관리하는 경우가 많다.\
예 Attack_Montage - Attack1, Attack2 ....\
Section을 나누면 원하는 것을 맞춰 쓸 수 있다.\
기존에 만들었던 Tag에 대응하는 FName과 일치해야 하는 것에 주의해야한다.\
합치고 Montage Sections에서 Clear 해야한다.\
넣은 순서대로 재생되기 때문에 Clear를 통해 다음 Section으로 자동 이동하는 연결을 제거한다.

주의: Play Montage는 단순 애니메이션 재생, Play Montage and Wait을 사용해야한다. 이번에 실수로 잘못써서 React Animation이 재생 안되는 문제가 생겼었다.

마지막으로 Montage Jump to Section을 사용하여 Section Name에 HitDirectionName(FName)을 연결하여 원하는 section으로 이동하게 만들었다.

참고: Start Section을 사용해도 원하는 Section부터 시작할 수 있다. 이것은 시작 section만 지정하는 것이다. Jump를 이용하면 확장성이 있어서 기다리고 재생, 조건에 만족하면 재생등이 가능하다. 보통 Jump를 쓴다고 한다.

```cpp
Play Montage

일반 Anim Montage 재생 함수
SkeletalMeshComponent를 직접 지정해야 한다.
몽타주만 재생하며 Gameplay Ability와는 연동되지 않는다.

Play Montage and Wait

Gameplay Ability 전용 Ability Task
Avatar의 Mesh를 자동으로 사용한다.
Ability가 몽타주 종료를 기다릴 수 있다.
Completed / Interrupted / BlendOut / Cancelled 등의 이벤트를 제공한다.
GAS에서는 일반적으로 이 노드를 사용한다.
```

Play Montage는 이럴 때 사용한다.\
Gameplay Ability 밖에서 몽타주를 재생할 때. ex. 죽음연출\
몽타주 종료를 기다릴 필요가 없을 때. ex. 문 열림 애니메이션

### 29. Primary Attack Particles


Primary Montage에서 Notifies를 하나 더 추가하고\
Play Niagara Particle Effect를 추가\
Niagara System에 준비된 이펙트를 넣고\
Location Offset을 조절하여 캐릭터의 공격 위치에 맞게 이펙트를 배치했다.


## Section 4: Attributes

### 30. Intro

### 31. Attribute Set


UAttributeSet class를 상속받아 클래스를 작성했다.\
AbilitySystem 폴더 아래 작성했다. 

우선 ATTRIBUTE_ACCESSORS 매크로를 우선 작성했다.\
AttributeSet.h에 주석으로 일반적인 사용법이 나와있다.
```cpp
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```
해당 매크로를 사용하면 BoilerPlate 작성을 덜어준다.\
GetHealthAttribute(),GetHealth(), SetHealth, InitHeath() 를 자동 생성해줌.


BoilerPlate
```cpp
#define ATTRIBUTE_ACCESSORS(ClassName,PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)

UCLASS()
class CRASHCOURSE_API UCC_AttributeSet : public UAttributeSet
{
	GENERATED_BODY()
public:
	virtual void GetLifetimeReplicatedProps(TArray<class FLifetimeProperty>& OutLifetimeProps) const override;
	
	UPROPERTY(BlueprintReadOnly,Replicated = OnRep_Health)
	FGameplayAttributeData Health;

	UFUNCTION()
	void OnRep_Health(const FGameplayAttributeData& OldValue);
	
	ATTRIBUTE_ACCESSORS(ThisClass,Health);
};

void UCC_AttributeSet::GetLifetimeReplicatedProps(TArray<class FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);
	
	DOREPLIFETIME_CONDITION_NOTIFY(ThisClass,Health,COND_None,REPNOTIFY_Always);
}
void UCC_AttributeSet::OnRep_Health(const FGameplayAttributeData& OldValue)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(ThisClass,Health,OldValue);
}
```

FGameplayAttributeData를 BlueprintReadOnly로 쓰는 이유\
일반적으로는 Gameplay Effect를 통해 변경되는 것이 권장되기 때문에.

복제되는 Attribute는 RepNotify를 구현해야한다.\
GAMEPLAYATTRIBUTE_REPNOTIFY는 GAS의 Prediction 및 Attribute 동기화가 정상적으로 동작하도록 연결해 준다.

복제되는 Attribute는 GetLifetimeReplicatedProps()에서 등록한다.

REPNOTIFY_Always는 복제 패킷이 받았을 때 값이 같더라도 RepNotify를 항상 호출한다.

FGameplayAttributeData  : GAS가 추적할 수 있는 Attribute 데이터. GameplayEffect가 이 값을 변경할 수 있다.

UPROPERTY(ReplicatedUsing = OnRep_Health) : 이 값이 서버에서 클라이언트로 복제될 때 OnRep_Health를 호출해라. 이것은 복제 가능한 변수임을 알리는 것 실제 등록은 아님.

void OnRep_Health(const FGameplayAttributeData& OldValue) : 클라이언트가 서버로부터 새 Health 값을 받았을 때 실행될 함수. 

GAMEPLAYATTRIBUTE_REPNOTIFY(UCC_AttributeSet, Health, OldValue) : Health 복제가 일어났다는 사실을 GAS 내부 시스템에 알려준다.\
예측 보정, 동기화

DOREPLIFETIME_CONDITION_NOTIFY : 실제 네트워크 복제 목록에 등록한다.\


| 코드 | 실행 위치 | 역할 |
|------|-----------|------|
| `FGameplayAttributeData Health` | 서버 / 클라이언트 | 실제 Attribute 값을 저장하는 데이터. Gameplay Effect가 이 값을 변경한다. |
| `UPROPERTY(ReplicatedUsing = OnRep_Health)` | 선언부 | Health가 복제될 때 `OnRep_Health()`를 호출하도록 지정한다. 복제 등록은 하지 않는다. |
| `void OnRep_Health(const FGameplayAttributeData& OldValue)` | 주로 클라이언트 | 서버로부터 새로운 Health를 받았을 때 실행된다. `OldValue`는 복제 전 값이다. |
| `ATTRIBUTE_ACCESSORS(ThisClass, Health)` | 서버 / 클라이언트 | `GetHealth()`, `SetHealth()`, `InitHealth()`, `GetHealthAttribute()` 등의 접근 함수를 자동 생성한다. |
| `GAMEPLAYATTRIBUTE_REPNOTIFY(ThisClass, Health, OldValue)` | 주로 클라이언트 | GAS 내부 시스템에 Health가 복제되었음을 알린다. Prediction 및 Attribute 동기화를 정상 처리한다. |
| `GetLifetimeReplicatedProps()` | 엔진(복제 정보 초기화) | 이 클래스에서 어떤 멤버를 네트워크 복제 대상으로 사용할지 등록하는 함수이다. 직접 호출하지 않으며 엔진이 사용한다. |
| `DOREPLIFETIME_CONDITION_NOTIFY(ThisClass, Health, COND_None, REPNOTIFY_Always)` | 엔진(복제 정보 초기화) | Health를 실제 복제 목록에 등록한다. `REPNOTIFY_Always`로 값이 같아도 `OnRep`를 항상 호출한다. |


```
서버
Health 변경
      │
      ▼
언리얼 Replication
      │
      ▼
클라이언트 Health 멤버 갱신
      │
      ▼
언리얼이 OnRep_Health() 호출
      │
      ▼
GAMEPLAYATTRIBUTE_REPNOTIFY()
      │
      ▼
ASC에게 "Health가 변경됐다" 전달
```


이해를 위한 추가: FGameplayAttributeData Replicated 와 DOREPLIFETIME_CONDITION_NOTIFY 만 해도 서버에서 값이 변경될 때 클라이언트의 AttributeSet의 Data도 변경은 된다. 하지만 GAS에서도 알리기 위해 OnRep 함수를 연결해서 GAMEPLAYATTRIBUTE_REPNOTIFY 를 호출하는 것이다.

### 32. Adding the Attribute Set


AttributeSet을 어디에 넣을 것인가?\
Player는 PlayerState, Enemy는 EnemyCharacter\
즉 일반적으로 ASC를 소유하는 객체에 함께 생성한다.

멤버 변수로 선언하고 생성자에서 CreateDefaultSubobject()로 생성한다.\
UPROPERTY()는 추가하지만 따로 속성을 넣진 않는다. AttributeSet은 Component가 아니라 UObject를 상속받는 객체이므로 Component 관련 설정은 사용하지 않는다.

참고 : GAS에서 ASC와 Attribute Set을 사용했을 때 Health, Mana와 같은 Attribute는 AttributeSet이 갖고 있게 된다. 그렇다면 HealthComponent, ManaComponent는 어디에 쓰느냐?\
보통 쓰지 않는다. 하지만 그렇다고 Component를 안쓰는 것은 아니다. 스탯이 아닌 기능은 여전히 Component를 사용한다.


### 33. Initialize Attribute Gameplay Effect


GAS에서는 Attribute를 C에서 직접 대입하는 것이 아니라 Gameplay Effect를 적용하는것이 표준적이다.\
영향을 준다는 의미에서 Effect로 지은듯하다.\
Attribute 초기화뿐 아니라 버프, 디버프, 데미지 등에도 사용된다.

Blueprint class의 Gameplay Effect로 부터 GE_InitializeAttributes를 생성했다.\
C++로도 구현할 수 있지만 BP에 존재하는 기능으로 거의 모든걸 다 할수 있어서 이게 보통인것 같다

일회성이므로 Duration policy를 Instant로 사용했다.\
필요에 따라 Infinite, HasDuration을 사용하면 된다.

Modifier로 어떻게 변경할 것인지 정의한다.\
기존값과 상관없이 쓸 것이므로 Override 옵션으로 진행했다.

Modifier Magnitude로 값을 스칼라, 다른 값 기반계산, 커스텀 계산, 커브 테이블 등을 사용 가능하다.\
단순 대입이므로 Scalable float으로 초기값을 설정했다.


Player와 Enemy 모두 사용할 수 있도록 BaseCharacter에 등록한다.
```cpp
UPROPERTY(EditDefaultsOnly)
TSubclassOf<UGameplayEffect> InitializeAttributesEffect;
```
이후 BP에서 GE_InitializeAttributes를 연결한다.


InitializeAttributes() 함수 구현\
1. GameEffect 존재 확인
2. EffectContext 생성
3. EffectSpec 생성
4. ApplyGameplayEffectSpecToSelf()

함수들은 ASC가 갖고 있다.

```cpp
// 2
FGameplayEffectContextHandle Context =
    ASC->MakeEffectContext();

// 3
FGameplayEffectSpecHandle Spec =
    ASC->MakeOutgoingSpec(
        InitializeAttributesEffect,
        1.f,
        Context);

// 4
ASC->ApplyGameplayEffectSpecToSelf(
    *Spec.Data.Get());
```

EffectContext: 어떤 상황에서 적용되는지 정보를 담는 객체. Instigator, Hit Result 등 저장 가능

EffectSpec: Gameplay Effect, Level, Context 등을 포함한 실제 적용 정보.


ASC->ApplyGameplayEffectSpecToSelf(
    *Spec.Data.Get());
Get을 통해 내부 Spec 포인터를 얻고 *로 역참조하여 Ref 전달.\
SpecHandle은 Spec 자체가 아니라 내부의 SharedPtr을 보유하는 Handle이다.


중요: 서버에서만 초기화 해야하므로\
Player의 PossessedBy()의 GiveStartupAbilities 아래에서\
Enemy의 BeginPlay()의 Authority 체크 아래에서

기본적으로 Authority 체크 아래 즉 서버에서만 하게 해야한다.


이번 강의에서는 사용 안하고 level을 1로 지정했지만, curve table을 사용하면\
level별로 hp를 몇으로 설정할지 등 가능하다.


디버그 확인: 콘솔에서 showdebug abilitysystem을 입력하면 값들을 확인할 수 있다.





추가: checkf(조건, TEXT("error message"))	// assert\
check(조건) assert만\
언리얼 전용 assert 매크로로 만들어져 있음\
언리얼 로그시스템에 연결되어있고 TEXT를 지원하므로 언리얼용 assert를 사용한다.

| 매크로 | 용도 |
|--------|------|
| `assert()` (C++) | 조건이 거짓이면 프로그램 중단 |
| `check()` (UE) | 반드시 참이어야 하는 조건 검사 |
| `checkf()` (UE) | `check` + 사용자 지정 에러 메시지 출력 |
| `ensure()` (UE) | 오류를 기록하지만 가능한 한 계속 실행 |
| `ensureMsgf()` (UE) | `ensure` + 사용자 지정 메시지 출력 |
