# デストラクタ
* map[meta header]
* std[meta namespace]
* multimap[meta class]
* function[meta id-type]

```cpp
~multimap();
```

## 概要
コンテナオブジェクトを破棄する。これは格納された要素のデストラクタを呼び出し、`multimap` によって確保された全てのアロケート済みストレージを解放する。


## 計算量
[`size()`](size.md) に対して線形時間（デストラクタ呼び出し）
