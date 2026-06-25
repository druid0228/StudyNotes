# UUserWidget

### Inheritance Hierarchy
UObject -> UVisual -> UWidget -> UUserWidget

---

UI화면 또는 UI조각을 나타내는 클래스\
UI에 관련된 대부분이 UUserWidget을 갖고 만들어짐


생성 및 사용
```cpp
CreateWidget<UHUDWidget>(
    GetWorld(),
    WidgetClass
);

Widget->AddToViewport();

```

생명주기 함수
```cpp
virtual void NativeConstruct() override  // BeginPlay와 비슷한 역할
virtual void NativeDestruct() override;
```

함수
```cpp
SetVisibility()
RemoveFromParent()
```

위젯 연결
```cpp
UPROPERTY(meta=(BindWidget))
UProgressBar* HealthBar;
```
meta=BindWidget하면 자동 연결\
BP 이름 = C++ 변수명 같아야 가능하다.\
직접 추가도 가능

Widget 안에 Widget 구조 가능
```cpp
MainHUD
    ├ HPWidget
    ├ InventoryWidget
    ├ SkillWidget
    └ ChatWidget
```

데이터는 PlayerState, Component, ViewModel 등 다른 객체가 가지고\
Widget은 표시를 담당하는 것이 일반적이다.

## UMG Layout

UMG는 Widget Tree 구조로 UI를 배치한다.

```text
MainHUD
└ Canvas Panel
   ├ HPWidget
   ├ MinimapWidget
   └ SkillPanel
      ├ SkillSlot1
      ├ SkillSlot2
      └ SkillSlot3
```

Panel Widget은 컨텐츠 Widget을 배치하는 곳
```
Canvas Panel     : 자유 배치, Anchor 사용
Vertical Box     : 세로 정렬
Horizontal Box   : 가로 정렬
Overlay          : 같은 위치에 겹쳐 배치
Grid Panel       : 격자 배치
Scroll Box       : 스크롤 영역
Size Box         : 크기 제한
Border           : 배경/테두리
```

일반적인 웹 UI의 Layout(Container)와 비슷한 개념이다.
필요에 따라 적절한 Panel Widget을 선택해서 사용하면 된다.