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