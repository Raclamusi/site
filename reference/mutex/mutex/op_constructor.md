# コンストラクタ
* mutex[meta header]
* std[meta namespace]
* mutex[meta class]
* function[meta id-type]
* cpp11[meta cpp]

```cpp
constexpr mutex() noexcept;   // (1)
mutex(const mutex&) = delete; // (2)
```

## 概要
- (1) : デフォルトコンストラクタ。`mutex`オブジェクトの初期化を行う。
- (2) : コピーコンストラクタ。コピー不可。


## 例外
この関数は、以下のerror conditionを持つ[`system_error`](/reference/system_error/system_error.md)例外オブジェクトを送出する可能性がある：

- [`resource_unavailable_try_again`](/reference/system_error/errc.md) : native handle型の計算ができない
- [`operation_not_permitted`](/reference/system_error/errc.md) : スレッドにこの操作を行う権限がない
- [`device_or_resource_busy`](/reference/system_error/errc.md) : native handle型の計算がロックされている
- [`invalid_argument`](/reference/system_error/errc.md) : ミューテックスを構築する一部のnative handle型計算が正しくない


## 備考
非トリビアルなコンストラクタが定義されるため、ムーブコンストラクタは定義されない。

デフォルトコンストラクタに`constexpr`が付加されていることにより、`mutex`クラスのオブジェクトは他のスレッド開始よりも前に初期化されることが保証される。

以下の例は`constexpr`が付加されていない場合にはおそらく動作しない。

## 例
```cpp example
// file a.cpp
#include <iostream>
#include <mutex>

std::mutex mutexA;
extern std::mutex mutexB;

class A {
public:
  A() {
    // デフォルトコンストラクタがconstexprでない場合、
    // 別のファイルで定義されているmutexBはこの時点では
    // 初期化されていないかもしれない。
    mutexB.lock();
    std::cout << "A" << std::endl;
    mutexB.unlock();
  }
} glbA;

int main()
{
}
```
* mutexB.lock()[link lock.md]
* mutexB.unlock()[link unlock.md]

```cpp
// file b.cpp
#include <iostream>
#include <mutex>

std::mutex mutexB;
extern std::mutex mutexA;

class B {
public:
  B() {
    // デフォルトコンストラクタがconstexprでない場合、
    // 別のファイルで定義されているmutexAはこの時点では
    // 初期化されていないかもしれない。
    mutexA.lock();
    std::cout << "B" << std::endl;
    mutexA.unlock();
  }
} glbB;
```
* mutexA.lock()[link lock.md]
* mutexA.unlock()[link unlock.md]

### 出力
```
A
B
```

```
B
A
```

## バージョン
### 言語
- C++11

### 処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): 4.7.0 [mark verified]
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): 2012 [mark verified], 2013 [mark verified], 2015 [mark verified]
    - 2012, 2013, 2015は、デフォルトコンストラクタに`constexpr`が修飾されていない。
    - 2012はコピーコンストラクタのdeleteに対応していないため、代わりにprivateで宣言のみ行う手法で代用されている。


## 関連項目
- [C++20 コンパイル時初期化を強制する`constinit`キーワードを追加](/lang/cpp20/constinit.md)


## 参照
- [Constant Initialization - 雑貨's tumblr](https://zakkas783-blog-blog.tumblr.com/post/25490513807/constant-initialization)
- [mutexのconstexprコンストラクタ - yohhoyの日記](http://d.hatena.ne.jp/yohhoy/20120621/p1)
- [なぜmutexオブジェクトはムーブできないか？ - yohhoyの日記](http://d.hatena.ne.jp/yohhoy/20161106/p1)
