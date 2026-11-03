# Unreal Engine 5 C++ Multiplayer Shooter

## Section 2: Creating a Multiplayer Plugin

### 4. Multiplayer Concepts

Multiplayer game의 근본 개념에 대해 배운다

Peer-to-Peer, Client-Server 그리고 언리얼이 무엇을 쓰는지 배운다.

멀티 플레이를 하기 위해서는 클라이언트끼리의 데이터 교환이 필요하다.\
다음으로 여러 방법을 본다.


1) Peer-to-Peer
플레이어끼리 직접 패킷을 전송한다.\
예를들어 이동을 하면 Player1에서 Player2에 MoveCharacter를 전송하고 반대도 마찬가지.\
작은 게임에는 괜찮지만 사람이 늘어날수록 모두가 서로에게 전송해야 하기 때문에 큰 게임에는 사용하기 힘들수 있다.\
또한 중앙 Authority가 없어 어느 게임 인스턴스의 상태가 올바른 것인지 판단하기 어렵다.

2) Client-Server
하나의 머신을 Server로 두고 Client들은 이 Server랑만 통신한다. 다른 클라이언트끼리 통신할 필요가 없어진다.\
서버가 Request를 받으면 요청을 검증하고 게임 상태를 변경한다, 문제 없으면 모든 클라이언트에 그 결과를 분배한다.\
이것이 우리가 알고있는 Replication이다.
    1) Listen-Server
        실제로 게임이 플레잉되고 랜더링도 하고 있는 서버\
        클라이언트가 서버를 겸한다고 보면 된다.\
        서버인 사람은 약간의 이득이 있다.\
        어느정도 작은 규모에서 충분하다
    2) Dedicated-Server
        서버는 게임을 플레이하지도 랜더링하지도 않는다.\
        일반적으로 게임 서버라고 상상하는 서버이다.\
        MMO등 규모가 큰 게임들은 Dedicated로 해야한다.

    
3) Unreal Engine Multiplayer
언리얼은 Authoritative Client-Server 구조를 사용하고있다.

서버가 게임 상태에 대한 최종 권한을 가지며, 서버의 상태를 올바른 상태로 간주한다.

프로젝트 구성에 따라 Listen Server 또는 Dedicated Server를 사용할 수 있다.

### 5. Testing Multiplayer

Unreal Engine에서 멀티플레이를 테스트하는 방법을 알아본다.

**1) PIE (Play In Editor)**

Unreal Editor에서는 별도의 패키징 없이 멀티플레이를 테스트할 수 있다.

Play 설정의 Multiplayer Options에서 다음 항목을 설정한다.

- **Number of Players:** 실행할 플레이어 인스턴스 수
- **Net Mode**
  - Play Standalone: 독립 실행, 서버에 자동 연결하지 않음
  - Play As Listen Server: 에디터가 Listen Server 역할을 수행하며 나머지 인스턴스는 Client로 접속
  - Play As Client: 백그라운드에 Dedicated Server를 생성하고 모든 플레이어 인스턴스가 Client로 접속

PIE를 이용하면 별도의 PC 없이 Listen Server와 Dedicated Server 환경을 테스트할 수 있다.

**2) LAN (Local Area Network)**

동일한 로컬 네트워크에 연결된 PC끼리 IP 주소를 이용해 멀티플레이 게임에 접속하는 방식이다.

강의에서는 `Lobby` 레벨을 생성하고 Character Blueprint에 다음 입력을 구현한다.

- **1 키 (호스트):** `Open Level (by Name)`
  - Level Name: `Lobby`
  - Options: `listen`
  - Lobby 레벨을 Listen Server로 실행하여 다른 플레이어의 접속을 허용한다.
- **2 키 (클라이언트):** `Execute Console Command`
  - Command: `open 192.168.x.x`
  - 해당 IP 주소의 서버에 접속한다.

서버 PC의 로컬 IP 주소는 Windows 명령 프롬프트에서 `ipconfig`로 확인할 수 있다.

**3) Packaging & Testing**

프로젝트를 Windows 대상으로 패키징하여 다른 PC에서도 실행할 수 있도록 한다.

- `Platforms → Windows → Package Project`
- 생성된 실행 파일뿐 아니라 관련 파일이 포함된 전체 패키징 폴더를 다른 PC로 복사한다.
- 호스트 PC에서 게임 실행 후 `1` 키로 Listen Server를 시작한다.
- 클라이언트 PC에서 게임 실행 후 `2` 키로 호스트에 접속한다.

이를 통해 실제 두 PC 사이에서 LAN 멀티플레이 동작을 확인할 수 있다.

다음 강의에서는 이러한 LAN 연결 과정을 C++로 구현한다.


### 6. LAN Connection

Lan 접속 BP에서 구현해 두었던 것을 C++에서도 구현해본다.


UFUNCTION(BlueprintCallable)
```cpp
void OpenLobby();

void CallOpenLevel(const FString& Address)

void CallClientTravel(const FString& Address)
```
이렇게 작성했다.

OpenLobby는 `World->ServerTravel("/Game/ThirdPerson/Lobby?listen")`를 호출했다.
- ServerTravel: 서버에서 레벨을 전환하며 연결된 클라이언트도 함께 이동시킨다.
- ?listen: Listen Server로 접속을 받을 수 있도록 설정하는 옵션이다.

경로는 level의 우클릭 copy path를 한 뒤에 Content 부분을 Game으로 바꾸고 그 이하를 사용하면 된다.

CallOpenLevel는 `UGameplayStatics::OpenLevel(this,*Address)` 를 호출했다.

CallClientTravel에서는 `GetGameInstance()->GetFirstLocalPlayerController()`로 PlayerController를 얻고\
`PlayerController->ClientTravel(Address,TRAVEL_Absolute)`를 호출했다.

이후 Package Project를 해서 생성된 exe에서\
로비를 열고 Client들이 참가 하는 것을 확인했다.

주의: 강의와 다르게 exe에서 로비 생성이 안되었었음. Lobby Map이 포함되지 않았던 것으로 추정\
Project Settings → Packaging 에서 Cook everything in the project content directory 체크


정리
* ServerTravel: 서버가 레벨을 전환하고, 현재 연결된 클라이언트들도 함께 이동시킨다.
* OpenLevel: 현재 게임 인스턴스에서 새로운 맵을 열거나 지정한 IP의 서버에 접속한다. 기존 World를 새로 로드하는 Non-Seamless Travel 방식이다.
* ClientTravel: 특정 PlayerController를 기준으로 클라이언트를 다른 맵이나 서버로 이동시킨다. 클라이언트가 직접 호출하거나 서버가 특정 클라이언트의 이동을 지시할 때 사용한다.