# コンストラクタ
* variant[meta header]
* std[meta namespace]
* bad_variant_access[meta class]
* function[meta id-type]
* cpp17[meta cpp]

```cpp
bad_variant_access() noexcept;           // (1) C++17
constexpr bad_variant_access() noexcept; // (1) C++26

bad_variant_access(const bad_variant_access&) noexcept;           // (2) C++17
constexpr bad_variant_access(const bad_variant_access&) noexcept; // (2) C++26
```

## 概要
`bad_variant_access`オブジェクトを構築する。

- (1) : デフォルトコンストラクタ
- (2) : コピーコンストラクタ


## 例外
投げない


## 関連項目
- [C++26 定数評価での例外送出を許可](/lang/cpp26/allowing_exception_throwing_in_constant-evaluation.md)


## 参照
- [P3378R2 `constexpr` exception types](https://open-std.org/jtc1/sc22/wg21/docs/papers/2025/p3378r2.html)
    - C++26で`constexpr`対応した
