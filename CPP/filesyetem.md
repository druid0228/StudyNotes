# Filesystem

## C++17

std::filesystem은 파일과 폴더를 다룰 수 있다.
이전에는 파일 존재확인, 폴더 생성, 폴더 순회 등을 하기 위해 여러 가지를 섞어 썻기 때문에
편하게 쓰기 위해 나왔다.

이전에는 OS API나 라이브러리를 사용해야 하는 경우가 많아
플랫폼별 코드가 작성되거나 구현이 복잡해질 수 있었다고 한다.

예시
```cpp
namespace fs = std::filesystem;

int main()
{   // ./Config/ 에서 .ini 확장자의 파일들을 찾아 출력하는 예제
    fs::path ConfigDir = "Config";  
    if (!fs::exists(ConfigDir)) return

    for (const auto& Entry :
         fs::recursive_directory_iterator(ConfigDir))
    {
        if (!Entry.is_regular_file())continue
        if (Entry.path().extension() != ".ini")continue
        // 파일을 읽으려면 ifstream이 필요
        std::ifstream File(Entry.path());   
        if (!File)continue

        while (std::getline(File, Line))
            std::cout << Line << "\n"
    }
}
```
참고: filesystem은 파일과 폴더를 찾고 순회하는 기능을 갖고 있고 파일의 내용을 읽고 쓰지는 못한다, 그러므로 fstream과 같이 쓰이는 경우가 많다.

상대 경로는 current_path() 기준으로 해석된다.
path는 ../../ 와 같이 상대 경로를 참조 가능하다.


---
함수 목록
```cpp
    fs::path
    path.stem()
    path.extension()
    fs::exists(path)
    fs::current_path()
    fs::create_directory()
    fs::remove()
    fs::recursive_directory_iterator(path)
    ...
```
경로, 존재 및 타입확인, 파일정보, 생성, 삭제, 복사, 이동, 순회,
비교, 권한, 상태, 등등..
파일 시스템관련 해서 필요한 함수들은 대부분 다 구현 되어 있으니
필요할 때 찾아서 사용하면 될 것 같다.

---

추가 : 경로 결합을 지원한다.

```cpp
    fs::path dir = "Assets";
    fs::path file = "Player.png";

    auto full = dir / file;
```
filesystem에서 / 연산자는 경로 결합을 위해 오버로딩되어 있다.
"a_str" + "b_str" 이렇게 할 필요가 없으므로 코드가 깔끔하게 된다.

추가 : 경로 결합은 운영체제에 맞는 경로 구분자를 자동으로 처리해준다고 한다.
/ 와 \ 를 알맞게 처리해준다고 하니 아주 편리 해보인다.
