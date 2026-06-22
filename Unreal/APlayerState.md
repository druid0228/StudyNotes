# APlayerState

### Inheritance Hierarchy
AActor -> AInfo -> APlayerState

---

모든 플레이어가 알아야하는 플레이어 상태 정보.\
PlayerController가 플레이어의 조종자라면
PlayerState는 플레이어의 공개 프로필에 가깝다.\
Pawn이 죽거나 교체되어도 PlayerState는 유지된다.

비교
| 이름               | 서버 | 본인 클라 | 타인 클라 |
|:------------------|:----:|:------:|:---------:|
|GameMode           |O     |X       |X          |
|PlayerController   |O     |O       |X          |
|PlayerState        |O     |O       |O          |


닉네임, kda, score 등에 필요.


```cpp
GameState->PlayerArray

└─ PlayerArray
   ├─ PlayerState A
   ├─ PlayerState B
   └─ PlayerState C

```