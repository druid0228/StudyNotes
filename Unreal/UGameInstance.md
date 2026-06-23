# UGameInstance

### Inheritance Hierarchy
UObject -> UGameInstance

---

게임 실행부터 종료까지 살아있는 객체.\
일종의 전역변수로 생각하면 될 것이다.

GameMode와 다르게 게임 중에 계속 유지.\
레벨이 변경되어도 유지된다.

대표 용도\
로그인정보, 계정정보, 로비데이터 등..

중요: ServerGameInstance, ClientGameInstance 따로 존재 자동 동기화 안됨

게임 종료 후에도 유지되어야 하는 정보는 SaveGame이나 외부 저장소(DB 등)에 저장해야 한다.
