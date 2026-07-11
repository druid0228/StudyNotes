# Prefab

미리 만들어 둔 GameObject 템플릿\
Asset이다. 실제로 디스크에 저장되는 파일이다.

Prefab은 GameObject 템플릿이며,\
Instantiate하면 해당 Prefab을 기반으로 새로운 GameObject Instance가 생성된다.

GameObject와 Component 구성 및 public Field와 [SerializeField]가 붙은 Field의 값을 저장한다.

Prefab을 수정하면 Override되지 않은 Instance들에 변경 사항이 반영된다.\
Override한 값은 유지된다.


## Instantiate
prefab은 보통 
```C#
Instantiate(EnemyPrefab);
```
으로 생성한다.
Instantiate의 결과는 실제 객체(Instance)다.

---

추가: SerializeField 값을 저장하기 때문에,
SerializeField가 붙지 않은 private 값은 변경해도 Instance들에 반영되지 않는다.
```C#
[SerializeField]
private int MaxHP = 100;

private int CurrentHP = 100;
```
prefab에서 MaxHP를 300으로 설정하면 Instance들은 300으로 변경되지만,
CurrentHP는 그런식으로 할 수 없다.

즉 C# 객체는 먼저 멤버 변수의 기본값으로 생성되고,

이후 Unity Serialization 대상(Field)에 대해서만\
Prefab(Scene)에 저장된 값으로 덮어쓴다.