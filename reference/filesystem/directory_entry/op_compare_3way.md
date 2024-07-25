# operator<=>
* filesystem[meta header]
* std::filesystem[meta namespace]
* directory_entry[meta class]
* function[meta id-type]
* cpp20[meta cpp]

```cpp
strong_ordering operator<=>(const directory_entry& rhs) const noexcept; // (1) C++20
```

## 概要
`directory_entry`オブジェクトを参照比較する。


## 戻り値
```cpp
return path() <=> rhs.path();
```
* path()[link path.md]


## 例外
投げない


## 備考
- この演算子により、以下の演算子が使用可能になる：
    - `operator<`
    - `operator<=`
    - `operator>`
    - `operator>=`


## 例
```cpp example
#include <iostream>
#include <filesystem>

namespace fs = std::filesystem;

int main()
{
  fs::directory_entry x{"a.txt"};
  fs::directory_entry y{"a.txt"};

  if ((x <=> y) == 0) {
    std::cout << "equal" << std::endl;
  }
}
```

### 出力
```
equal
```

## バージョン
### 言語
- C++20

### 処理系
- [Clang](/implementation.md#clang):
- [GCC](/implementation.md#gcc): 10 [mark verified]
- [Visual C++](/implementation.md#visual_cpp): ??


## 参照
- [P1614R2 The Mothership has Landed](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p1614r2.html)
    - C++20での三方比較演算子の追加と、関連する演算子の自動導出