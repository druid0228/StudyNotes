# as_const

## C++17

`std::as_const`는 객체를 const 참조(const&)로 반환한다.

```cpp
std::string Name = "Alice";

auto& A = Name;
const  auto& B = std::as_const(Name);
```

원본 객체는 그대로이고, const 참조로만 접근하게 한다.