# page_title メソッド
```
def page_title(title = '', admin: false)
  base_title = admin ? 'BOARD APP(管理画面)' : 'BOARD APP'
  title.present? ? "#{title} | #{base_title}" : base_title
end
```
### 何をしている？
- ブラウザタブの <title> を整えるためのメソッド。
- 引数：
  - title … ページごとのタイトル（例: 掲示板タイトル）
  - admin: false … 管理画面かどうかを指定するフラグ

### 処理の流れ

1. base_title を決定
  - 管理画面なら → "BOARD APP(管理画面)"
  - そうでなければ → "BOARD APP"
2. title が渡されていれば → "ページタイトル | BOARD APP" の形にする
3. 渡されていなければ → "BOARD APP" だけ返す

👉 つまり 「共通のタイトル規則」を一箇所にまとめている。

# 補足
書かれているコード
```
title.present? ? "#{title} | #{base_title}" : base_title
```
分解して説明
1. title.present?
- title に値が入っていて空じゃないか？を判定
-  Rails のメソッドで、nil や "" なら false を返す

2. ? 以降と : の前まで
- true の場合に返す値
- この場合は #{title} | #{base_title}
- 例： title = "掲示板詳細" なら → "掲示板詳細 | BOARD APP"

3. : 以降
- false の場合に返す値
- この場合は base_title そのまま
- 例：title = nil や "" なら → "BOARD APP"

```
三項演算子の形

条件式 ? 真の場合の値 : 偽の場合の値


このコードは
タイトルがあれば → "タイトル | アプリ名"
タイトルがなければ → "アプリ名"
```
