# AHUD

### Inheritance Hierarchy
UObject -> AActor -> AHUD

---

초기 ue3~4 시절에는 DrawText등으로 직접 그렸고.\
HUD는 Canvas 였다.

현대의 HUD는 Widget 생성 및 관리 역할로 주로 사용된다.\
로컬 플레이어의 PlayerController에 연결된다.

HUD는 주로 표시를 담당한다.

Widget을 HUD가 아닌 PlayerController에서 직접 생성 및 관리하는 방법도 가능하다.

예시
```cpp
void AMyHUD::BeginPlay()
{
    Super::BeginPlay();

    MainWidget =
        CreateWidget<UUserWidget>(
            GetWorld(),
            MainWidgetClass
        );

    MainWidget->AddToViewport();
}
```

꼭 쓰지 않아도 되지만 HUD를 그냥 써도 좋은 이유들

장점:\
GameMode의 HUDClass로 자동 생성됨\
PlayerController에서 GetHUD()로 표준 접근 가능\
UI 관련 코드를 PlayerController에서 분리 가능\
DrawHUD/Canvas 디버그 그리기 가능



