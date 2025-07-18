# コンストラクタ
* random[meta header]
* std[meta namespace]
* random_device[meta class]
* function[meta id-type]
* cpp11[meta cpp]

```cpp
explicit random_device(const string& token = implementation-defined); // (1)
random_device() : random_device(implementation-defined) {}            // (1) C++20

explicit random_device(const string& token);                          // (2) C++20

random_device(const random_device&) = delete;                         // (3)
```
* string[link /reference/string/basic_string.md]

## 概要
- (1) : デフォルトコンストラクタ
    - C++17まで : トークンを受け取って乱数生成器を構築する。デフォルトのトークンパラメータは実装定義である。
    - C++20 : 実装定義のデフォルトトークンパラメータによって(2)に委譲。
- (2) : トークンを受け取って乱数生成器を構築する。
    - ※実装は、このトークンパラメータによって、異なるランダムのソースを使用してもよい。
- (3) : コピーコンストラクタ。コピー禁止。
    - これによって、ムーブコンストラクタも自動生成されない。

### 有効なトークン
- Windows
    - Visual C++: 無視される
    - Clang: `"/dev/urandom"` (これ以外を与えると例外が送出される)  
      ただし [`rand_s`](https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/rand-s) に使われることはないので指定する必要はない
    - GCC (MinGW): 文字列化した整数  
      ただし実体は [`mt19937`](../mt19937.md) なので**使用を推奨しない**
- Unix 系
    - Clang (libc++): `"/dev/urandom"` (デフォルト) または `"/dev/random"`
    - GCC (libstdc++): `"default"` (デフォルト)、`"/dev/urandom"` または `"/dev/random"`  
      デフォルトでは、CPU の `RDRAND` 命令が使用できれば (`_GLIBCXX_X86_RDRAND` が定義されていれば) それを、そうでなければ `/dev/urandom` から値を取得する

## 例外
- (1), (2) : 乱数生成器を初期化できなかった場合、[`exception`](/reference/exception/exception.md)から派生した実装定義の例外オブジェクトを送出する


## 例
```cpp example
#include <random>

int main()
{
  // (1) デフォルト構築
  {
    std::random_device rd;
  }

  // (1) トークン指定
  {
    // UNIX系OS上固有で、ランダムソースを/dev/randomにする
    std::random_device rd("/dev/random");
  }
}
```


### 出力
```
```

## バージョン
### 言語
- C++11

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): 4.7.2 [mark verified]
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): 2010 [mark verified], 2012 [mark verified], 2013 [mark verified], 2015 [mark verified], 2017 [mark verified]


## 参照

- [P0935R0 Eradicating unnecessarily explicit default constructors from the standard library](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2018/p0935r0.html)
