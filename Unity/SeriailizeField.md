# SerializeField

Unity의 Reflection + Inspector 노출 기능

본질적인 역할: 직렬화 대상에 등록하는 것.

```C#
public class Player : MonoBehaviour
{
    [SerializeField]
    private int HP = 100;

    public int MP = 50;

    public static int Count;
}
```

MonoBehaviour나 ScriptableObject 등 Unity가 직렬화하는 객체에서는 public 인스턴스 Field가 기본적으로 직렬화된다.\
private 부분에 대해서만 Inspector에 보이기 위해 `[SerializeField]` 를 사용하는 것이다.

추가: static은 직렬화하지 않는다. 함수는 직렬화하지 않는다.\
Field만 검사한다.

작동 순서
```
[SerializeField]
↓
Unity Serialization 대상 등록
↓
씬/Prefab 저장 가능
↓
Inspector에도 표시
```

중요: C# Reflection과 Unity Serialization은 서로 다른 개념이다.

C#은 기본적으로 Reflection을 제공하며, 이는 타입(Type), Field, Method 등의 정보를 런타임에 조회하는 기능이다.

반면 Unity에서 Inspector에 표시하거나 Scene/Prefab에 저장하는 기능은 Reflection이 아니라 Unity Serialization 시스템이 담당한다.

즉, 'Unity Reflection'이라는 별도의 개념은 없으며, Inspector 노출과 직렬화는 Unity Serialization 규칙에 의해 동작한다.