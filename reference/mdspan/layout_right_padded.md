# layout_right_padded
* mdspan[meta header]
* class template[meta id-type]
* std[meta namespace]
* cpp26[meta cpp]

```cpp
namespace std {
  template<size_t PaddingValue = dynamic_extent>
  struct layout_right_padded {
    template<class Extents>
    class mapping;
  };
}
```
* dynamic_extent[link /reference/span/dynamic_extent.md]
* Extents[link extents.md]
* mapping[link layout_right_padded/mapping.md.nolink]

## 概要
`layout_right_padded`は、多次元配列ビュー[`mdspan`](mdspan.md)に対して、パディングあり行優先(row major)[レイアウトマッピング](LayoutMapping.md)を表現するポリシークラスである。

全要素が隣接配置される[`layout_right`](layout_right.md)とは異なり、`layout_right_padded`では最右次元の隣次元（第R-2次元）ストライド幅`stride(`[`extents_type::rank()`](extents/rank.md) `- 2)`が最右次元の要素数`extent(extents_type::rank() - 1)`よりも大きい、つまり第R-2次元においてパディングが挿入される可能性がある。

`layout_right_padded`の特殊化は、[レイアウトマッピングポリシー要件](LayoutMappingPolicy.md)を満たす[トリビアル型](/reference/type_traits/is_trivial.md)である。


## メンバ型

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`mapping`](layout_right_padded/mapping.md.nolink) | レイアウトマッピング | C++26 |


## 例
```cpp example
#include <mdspan>
#include <print>

int main()
{
  double arr[] = {1, 2, 3, 0, 4, 5, 6, 0};
  // 1 2 3 -
  // 4 5 6 -

  // 要素数2x3の2次元配列／行優先レイアウト／アライメント4
  using Ext2x3 = std::extents<size_t, 2, 3>
  std::mdspan<double, Ext2x3, std::layout_right_padded<4>> mat{arr};

  for (size_t i = 0; i < mat.extent(0); ++i) {
    for (size_t j = 0; j < mat.extent(1); ++j) {
      std::print("{}{}", (j ? " " : ""), mat[i, j]);
    }
    std::println();
  }
}
```
* std::layout_right_padded[color ff0000]
* extent[link mdspan/extent.md]

### 出力
```
1 2 3
4 5 6
```


## バージョン
### 言語
- C++26

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): ??
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): ??


## 関連項目
- [`layout_right`](layout_right.md)


## 参照
- [P2642R5 Padded mdspan layouts](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2023/p2642r5.html)