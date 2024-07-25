# 動的メモリ確保の省略の許可
* cpp14[meta cpp]

<!-- start lang caution -->

このページはC++14に採用された言語機能の変更を解説しています。

のちのC++規格でさらに変更される場合があるため[関連項目](#relative-page)を参照してください。

<!-- last lang caution -->

## 概要

以前の仕様では、`new`式による動的メモリ確保はコードに書かれた通りに実行されなければならず、ひとまとめにしたり省略したりすることはできなかった。

メモリ確保の最適化のためにこの制限は緩和され、実装は`new/delete`の呼び出しをまとめたり省略したりすることができるようになる。

```cpp
void lump() {
  // 個別のnew/deleteの呼び出しを
  int* p1 = new int{1};
  int* p2 = new int{2};
  int* p3 = new int{3};

  delete p1;
  delete p2;
  delete p3;

  // このようにまとめることが許可される
  int* p = new int[3]{1, 2, 3};

  delete[] p;
}

void emit() {
  // 確保サイズが分かっているようなnewの呼び出しを
  int* p = new int{10};
  delete p;

  // 通常の変数宣言のように置き換えても良い
  int n = 10;
}
```

ただし、このようなまとめと省略は最適化の一環として許可されているものに過ぎず、必ず行われるわけではない。

また、これらの機能と直接関係するものではないが、ユーザー定義されたものも含めた`operator new, operator delete`およびCライブラリの`malloc, calloc, free, realloc`の呼び出しはデータ競合を起こさない事が規定された。

## 仕様

実装は、オーバーロード可能なグローバルの割り当て関数（[`::operator new`](/reference/new/op_new.md)/[`::operator new[]`](/reference/new/op_new[].md)）の呼び出しを省略できる。その場合、（確保されるはずだった）ストレージは実装によって提供されるか、別の`new`式によるアロケーションを拡張してあてがわれる。

ただし、`new`式の呼び出し`e1`のアロケーションを拡張して別の`new`式`e2`のストレージを提供する事ができるのは、アロケーションの拡張が行われなかった時にそれらが次の条件を全て満たしている場合に限る

1. `e1`の評価は`e2`の評価よりも前に順序づけられる
2. `e1`がストレージを確保するならば、`e2`が呼び出される
3. `e1`と`e2`は同じオーバーロード可能なグローバルの割り当て関数を呼び出す
4. `e1`と`e2`で呼び出される割り当て関数が例外を投げる場合、`e1`と`e2`のどちらの評価で発生した例外でも、まず同じハンドラでキャッチされる
5. `e1`と`e2`によって返されるポインタ値は、評価される`delete`式のオペランドである
6. `e2`の評価は、`e1`によって生成されたポインタ値をオペランドにとる`delete`式の評価の前に順序づけられる

```cpp
void ok () {
  try {
    int* e1 = new int{1};
    int* e2 = new int{2};

    delete e2;
    delete e1;
  } catch(...){}
}

void ng1 () {
  // NG、e2 -> e1の順で確保されている
  // この場合、e2を拡張してe1を省略することはできる
  int* e2 = new int{2};
  int* e1 = new int{1};

  delete e1;
  delete e2;
}

void ng2 (bool cond) {
  int* e1 = new int{1};
  
  // NG、e2は必ずしも評価されない
  if (cond) {
    int* e2 = new int{2};

    delete e2;
  }

  delete e1;
}

void ng3 () {
  // NG、同じ::operator newを呼び出さない
  int* e1 = new int{1};
  int* e2 = new int[]{2};

  delete e1;
  delete[] e2;
}

void ng4() {
  try {
    int* e1 = new int{1};

    // NG、最初にキャッチされるハンドラが異なる
    try {
      int* e2 = new int{2};
      delete e2;
    } catch (...) {}

    delete e1;
  } catch(...){}
}

void ng5() {
  // NG、片方または両方がdeleteされていない
  int* e1 = new int{1};
  int* e2 = new int{2};

  delete e2;
  //delete e1;
}

void ng6 () {
  int* e1 = new int{1};
  delete e1;
  
  // NG、e2の前にe1が解放されている
  int* e2 = new int{2};
  delete e2;
}
```

ここでのNGはコンパイルエラーとなるわけではなく、メモリ確保省略がなされないことを表している。

すなわち、`e2`の確保するメモリ領域の生存期間は`e1`のそれに完全に包含されており、どちらもきちんと`delete`され、同じ関数経由でメモリを確保している場合にのみ、`e1`と`e2`によるメモリ確保は統合される。

このような`new`式の呼び出し`e1`の割り当てが拡張された場合、拡張後に呼び出される割り当て関数の`size`パラメータ（要求サイズ）は元の`e1`と`e2`で指定されていた`size`の合計値に、確保された領域にオブジェクトをアラインさせるために必要なパディングサイズを加えた値を超えない。

`delete`式では、そのオペランドのポインタが割り当てが拡張された`new`式（`e1`）から返されたものであり、`e1`を拡張することによってストレージを提供されていた他の全ての`new`式（`e2`）から返されたポインタに対する`delete`式が評価済である場合、その`delete`式は`e1`を拡張して得られた領域を解放する。

そうではない場合の`delete`式、すなわち`e2`から返されたポインタに対する`delete`式は、解放関数（`::operator delete`）を呼び出さない（領域上のオブジェクトの破棄のみを行う）。

したがって、省略された`::operator new`呼び出しに対応する`::operator delete`の呼び出しもまた省略される。

## この機能が必要になった背景・経緯

動的メモリ確保と解放はとても重い処理であり、パフォーマンスの最適化のためには可能な限り回避する事が望ましい。ただし、呼び出しのミクロな最適化とメモリ割り当て戦略のマクロな最適化は区別される必要があり、それを考慮してメモリ確保を最適化するにはプログラムの実行時の振る舞いや、そこから提供されるヒントを考慮した最適化が必要となる。

しかしC++11の仕様に厳密に従えば、`new/delete`式の呼び出しはその呼び出しから直接得られる情報しか用いてはならず、`new`によるメモリ確保はコードに書いた通りに実行されなければならなかった。そのため、ミクロな最適化もマクロな最適化も妨げられており、動的メモリ確保・解放処理の最適化を阻害していた。

C++11時点で既に、そのような最適化を行うメモリアロケータ（[TCMalloc](http://goog-perftools.sourceforge.net/doc/tcmalloc.html)）やコンパイラが存在しており、それらの存在を追認しかつ動的メモリ確保・解放処理の更なる最適化を可能とするために規格書の文面を調整することとなった。

それによって、ミクロな範囲での`new/delete`式の省略が許可され、マクロな範囲の様々な情報を考慮してそれを行う事が可能となった。

## <a id="relative-page" href="#relative-page">関連項目</a>

- [C++20 可変サイズをもつコンテナの`constexpr`化](/lang/cpp20/more_constexpr_containers.md)


## 参照

- [N3664 Clarifying Memory Allocation](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3664.html)
- [C++14言語編 - slideshare](https://www.slideshare.net/kariya_mitsuru/c14-84084931)