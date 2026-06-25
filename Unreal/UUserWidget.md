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