## Specの書き方
```ruby
describe [仕様を記述する対象（テスト対象）], type: [Specの種類] do

  context [ある状況・状態] do
    before do
      [事前準備]
    end

    it [仕様の内容(機体の概要)] do
      [期待する動作]
    end
  end

end

```

---

## 🔹 一行ずつの解説

### `describe [仕様を記述する対象（テスト対象）], type: [Specの種類] do`

* **describe** は「テストの大枠」を定義する。
* ここに「どのクラスや機能をテストするか」を書く。
* 例: `describe Task, type: :model do`
  👉 Taskモデルに関するテストが始まる。

---

### `context [ある状況・状態] do`

* **context** は「特定の条件や状況」を表す。
* describe の中でさらに「どういう場合か」を分けて整理できる。
* 例: `context 'titleが空の場合' do`
  👉 「titleがない状況」をまとめてテストする。

---

### `before do ... end`

* **before** は「テストを実行する前に、毎回共通で実行したい処理」を書く場所。
* 例:

  ```ruby
  before do
    @user = User.create!(email: "user@example.com", password: "password")
  end
  ```

  👉 こうしておけば、各テスト内で毎回ユーザーを作る必要がなくなる。

---

### `it [仕様の内容(期待の概要)] do ... end`

* **it** は「1つのテストケース」を定義する。
* `'◯◯であること'` のように「何を確認するテストか」を説明文として書く。
* 中には `expect(...).to ...` を使って検証処理を書く。

例:

```ruby
it 'titleが空だと無効であること' do
  task = Task.new(title: nil)
  expect(task).to be_invalid
end
```

👉 このテストは「titleが空のとき、Taskは無効になる」ことを確認している。

---

## ✅ まとめ
### テストケースを整理や分類する
* **describe** → テスト対象をまとめる大枠
* **context** → 状況や条件でグループ化
### テストコードを実行する
* **before** → テスト実行前の共通準備
* **it** → 1つのテストケース（期待される動作を確認する）

---
