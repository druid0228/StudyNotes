# AGameMode

### Inheritance Hierarchy
AActor -> AInfo -> AGameModeBase -> AGameMode

---

AGameMode는 게임의 rule을 정의하는 객체이다.
AGameMode는 서버 전용 객체이다.
멀티플레이 기준 서버에만 존재한다.

또한 GameMode는 맵단위로 새로 생성한다.
전역을 유지하고 싶다면 GameInstance를 사용한다.

UE에서 GameMode BP를 보면 DefaultPawnClass,
PlayerControllerClass,
HUDClass,
PlayerStateClass, ... 등등을 연결 가능한데
GameMode가 이러한 기본 클래스들의 생성에 관여 한다.

참고:
AGameMode는 서버 전용 객체이지만 싱글 플레이에서도 존재한다.

싱글 플레이는 별도의 서버와 클라이언트가 존재하는 것이 아니라
하나의 프로세스가 서버 역할과 클라이언트 역할을 동시에 수행한다.

따라서 싱글 플레이에서도 GameMode가 생성되고 동작한다.