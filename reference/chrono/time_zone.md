# time_zone
* chrono[meta header]
* std::chrono[meta namespace]
* class[meta id-type]
* cpp20[meta cpp]

```cpp
namespace std::chrono {
  class time_zone;
}
```

## 概要
`time_zone`は、指定した地理領域の全てのタイムゾーン遷移を表すクラスである。

地域間には時差があるため、地球上の各地域に「タイムゾーン (時間帯)」が割り振られ、標準時であるUTCタイムゾーンからの時差が定義される。

このクラスは、UTCタイムゾーンから特定のタイムゾーン、およびその逆の時間変換を行う。

タイムゾーン付きの時間点として[`zoned_time`](zoned_time.md)クラスが定義されているため、基本的にはそちらを使うことを推奨する。


## メンバ関数
### 構築／コピー／破棄

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| `time_zone(time_zone&&) = default;`<br/> `time_zone() = delete;`<br/> `time_zone(const time_zone&) = delete;` | コンストラクタ | C++20 |
| `time_zone& operator=(time_zone&&) = default;`<br/> `time_zone& operator=(const time_zone&) = delete;` | 代入演算子 | C++20 |

ここに記載されていない追加のコンストラクタは未規定である。ユーザーがこのクラスのオブジェクトを構築することはできず、タイムゾーンデータベース[`tzdb`](tzdb.md.nolink)が構築を行う。


### 観測

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`name`](time_zone/name.md.nolink)         | タイムゾーン名を取得する | C++20 |
| [`get_info`](time_zone/get_info.md.nolink) | タイムゾーンの情報を取得する | C++20 |


### 変換

| 名前 | 説明 | 対応バージョン |
|------|------|----------------|
| [`to_sys`](time_zone/to_sys.md.nolink)     | ローカル時間からシステム時間に変換する | C++20 |
| [`to_local`](time_zone/to_local.md.nolink) | システム時間からローカル時間に変換する | C++20 |


## 例
```cpp example
```

### 出力例
```
```

## バージョン
### 言語
- C++20

### 処理系
- [Clang](/implementation.md#clang): (9.0時点で実装なし)
- [GCC](/implementation.md#gcc): (9.2時点で実装なし)
- [Visual C++](/implementation.md#visual_cpp): (2019 Update 3時点で実装なし)
