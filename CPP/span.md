# Span

## C++20

연속된 메로리(Contiguous Memory)를 참조할 수 있는 view객체
예 int [n], array<int>(n), vector<int>(n)

나온 이유는 함수의 인자로 f(Data,size)로 넘겼을 때
배열의 사이즈보다 큰 사이즈로 넘겨도 컴파일이 되어서
data + size를 별도로 넘기는 실수를 줄이고
범위를 명시적으로 표현하기 위해 도입되었다.

참고: data+count의 묶음. string_view와 비슷 하지만 자료 변경 가능
그러나 동일하게 소유권은 없다. 가리키는 객체가 clear 되었을 때 액세스하면 UB문제가 생길 수 있음

아래 함수 등을 지원
```cpp
    size()
    data()
    first()
    last()
    subspan()
```

보통 vector<int> arg를 함수 인자를 넘기면 내부에서 size 함수를 써서\
꼭 필요한가 싶지만 안전하게 넘기는 방법이 된다.

편리한점
```cpp
    void Function(std::span<const Vertex> Vertices)
    
    위와 같은 코드가 존재한다면

    std::vector<Vertex>
    std::array<Vertex,100>
    Vertex Buffer[100]
```
어느 형태의 인자도 동일하게 넘길 수 있다.\
연속적인 Vertex 자료를 넘긴다는 뜻이다.\
필요에 따라 일반 배열에서 vector로 변경하더라도
함수의 정의를 바꿀 필요가 없어진다.

span은 사이즈가 기록된 포인터로 볼 수 있고 사이즈가 작다\
그러므로 연속된 배열 자료에 대해서 함수 인자는 std::span 을 사용하는 것이 안전하고 편리하다.

실제 쓰기 좋은 예시
```cpp
    void ParseHeader(std::span<const uint8_t> Header){...}

    std::span<const uint8_t> PacketView(Packet)
    auto Header = PacketView.first(16)
    auto Payload = PacketView.subspan(16)

    Parse(Packet.subspan(16, 112))
```
연속된 자료형에 대해 begin end에 size를 쓰는 것 보다
좀더 깔끔하고 명확하게 의도 파악이 가능하다.

새로운 기능은 아니지만 범위에 관련된 더 좋은 인터페이스다.