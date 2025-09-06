# 日付範囲検索（シンプルな単一条件）

実際の実装コード
```
# config/initializers/ransack.rb
Ransack.configure do |config|
  config.add_predicate 'lteq_end_of_day',
                       arel_predicate: 'lteq',
                       formatter: proc { |v| v.end_of_day }
end
```
各パラメータの詳細解説

1. predicate名の設定
config.add_predicate 'lteq_end_of_day'

役割: カスタムPredicateの名前を定義
使用場面: ビューで created_at_lteq_end_of_day として使用
命名規則: {カラム名}_{predicate名} の形で呼び出される

補足
- predicateは、条件を判断する部分を指している

2. arel_predicate（必須）
arel_predicate: 'lteq'

役割: 実際のSQL条件を指定
lteq: "Less Than or Equal" (以下)
他の選択肢: eq(等しい), gteq(以上), matches(LIKE), in(IN句)など

補足
- Arel = SQLを作るためのRubyの仕組み(A Relational Algebra)
- arel_predicateは、ArelがSQLを作るときに使う、実際の条件の種類

3. formatter（重要）
```
formatter: proc { |v| v.end_of_day }
#          ↑   ↑ ↑ ↑---|
#        手順書 箱 材料 加工方法
```
役割: 入力値を変換してからデータベースに送る
v.end_of_day: 日付を「その日の23:59:59.999...」に変換
例: 2024-01-15 → 2024-01-15 23:59:59.999999999

補足
- formatter: proc = 「データを加工する手順書」
  - formatter は「データを整形する役割」
  - proc は「処理の手順を保存する箱」

- |v| は「入力されるデータ」を表す変数
- v.end_of_day は「実際の加工処理」

結論：「手順書の箱に、材料を加工する方法を入れておく」 というイメージ


なぜこのカスタマイズが必要なのか？
問題の背景
```
# 通常のlteqの場合
created_at_lteq: '2024-01-15'

# ↓ 実行されるSQL
WHERE created_at <= '2024-01-15 00:00:00'

# → 2024-01-15の投稿は検索結果に含まれない！
カスタムPredicateの効果

# lteq_end_of_dayの場合
created_at_lteq_end_of_day: '2024-01-15'

# ↓ formatterで変換
WHERE created_at <= '2024-01-15 23:59:59.999999999'

# → 2024-01-15の投稿も検索結果に含まれる！
```

# 他のformatter例で理解を深めよう
例1: 文字列を大文字に変換
```
formatter: proc { |v| v.upcase }
# 動作例
'hello' → 'HELLO'
```

例2: 数値を2倍にする
```
formatter: proc { |v| v * 2 }
# 動作例
10 → 20
```

例3: 文字列の前後に%を追加（LIKE検索用）
```
formatter: proc { |v| "%#{v}%" }
## 動作例
'Rails' → '%Rails%'
```

なぜprocを使うの？
理由1: 処理を後で実行したいから
```
# 検索フォームで入力される
created_at_lteq_end_of_day: '2024-01-15'

# ↓ この時点でformatterが実行される
proc { |v| v.end_of_day }

# ↓ SQLが生成される
WHERE created_at <= '2024-01-15 23:59:59.999999999'
```

理由2: 動的に処理を変更できるから
```
# 設定で処理内容を変更可能
formatter: proc { |v| v.end_of_day }      # パターン1
formatter: proc { |v| v.beginning_of_day } # パターン2
```
