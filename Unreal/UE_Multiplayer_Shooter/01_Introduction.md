# Unreal Engine 5 C++ Multiplayer Shooter

## Section 1: Introduction

### 1. Introduction


UE5 C++을 사용하여 서버 권한(Server Authority) 기반의 멀티플레이 슈팅 게임을 처음부터 제작하는 강의다.

주요 학습 내용
- Multiplayer Plugin: Online Subsystem과 Steam을 활용한 세션 생성, 검색, 참가 및 종료
- Replication / RPC: 서버와 클라이언트 간 통신, 권한 및 데이터 동기화
- Combat System: 다양한 무기, 사격, 투사체, 데미지 및 애니메이션
- Game Framework: GameMode, GameState, PlayerState를 활용한 매치 진행 및 점수 관리
- Lag Compensation: Client-Side Prediction, Server-Side Rewind를 통한 지연 보상
- Network Optimization: 대역폭 최적화, 서버 검증 및 치트 방지
- Game Modes: Free-for-All, Team Match, Capture the Flag

### 2. About this Course

강의는 크게 두 부분으로 구성된다.
- Multiplayer Plugin: 온라인 세션 생성 및 관리 기능을 제공하는 독립적인 플러그인 제작
- Game Project: 캐릭터, 무기, 전투 시스템 등 실제 멀티플레이 게임 구현
두 부분은 독립적이므로 원하는 순서로 학습할 수 있다. Game Project부터 시작할 경우 제공되는 플러그인을 사용하면 된다.

강의 자료 및 참고 사항
- 각 강의별 GitHub Commit을 제공하여 코드 변경 사항을 비교할 수 있다.
- Discord 커뮤니티와 Udemy Q&A를 통해 질문 및 멀티플레이 테스트가 가능하다.
- 강의 중 선택적으로 수행하는 Challenge가 제공된다.


### 3. About the Testing Channel

Discord 멀티플레이 테스트 채널은 보안 문제로 폐쇄되었으며, 대신 PIE, LAN 또는 Windows Sandbox를 활용한 테스트를 권장한다.