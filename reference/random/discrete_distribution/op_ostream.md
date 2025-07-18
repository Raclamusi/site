# operator<<
* random[meta header]
* std[meta namespace]
* function template[meta id-type]
* cpp11[meta cpp]

```cpp
namespace std {
  template <class CharT, class Traits, class IntType>
  std::basic_ostream<CharT, Traits>& operator<<(
    std::basic_ostream<CharT, Traits>& os
    const discrete_distribution<IntType>& x);
}
```

## 概要
ストリームへの出力を行う。


## 効果
`os`に対して、分布オブジェクト`x`の現在状態を出力する。


## 事後条件
`os`のフォーマットフラグが、この関数を呼び出す前の状態に戻ること。


## 戻り値
`os`


## 例
```cpp example
#include <iostream>
#include <random>

int main()
{
  std::discrete_distribution<> dist = {0.1, 0.2, 0.3};
  std::cout << dist << std::endl;
}
```

### 出力例
```
3 1.66666666666666657e-01 3.33333333333333315e-01 4.99999999999999889e-01
```

## バージョン
### 言語
- C++11

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): 4.7.2 [mark verified]
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): ??


## 参照
