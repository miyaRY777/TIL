# Railsメソッド学習メモ: `form_with`

## 問題

Railsでフォームを作りたいです。
次の3つを `form_with` を使って実装してください。

1. ユーザーの新規登録フォーム（`name` と `email`）
2. 既存のユーザー編集フォーム（`@user` を利用）
3. 投稿フォームを非同期通信（Ajax/Turbo）で動かすフォーム

---

## 回答例
```erb
<!-- 1. 新規登録フォーム -->
<%= form_with url: users_path, local: true do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>

  <%= f.label :email %>
  <%= f.email_field :email %>

  <%= f.submit "登録" %>
<% end %>

<!-- 2. 編集フォーム -->
<%= form_with model: @user, local: true do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>

  <%= f.label :email %>
  <%= f.email_field :email %>

  <%= f.submit "更新" %>
<% end %>

<!-- 3. 非同期フォーム（Rails 7 のデフォルト） -->
<%= form_with model: @post do |f| %>
  <%= f.label :body %>
  <%= f.text_area :body %>
  <%= f.submit "投稿" %>
<% end %>
```

---

## 解説

* `form_with` は Rails でフォームを作成するためのメソッド
* 基本形:`form_with model: @user, local: true`(この形がよく使われる)
  - model: … モデルオブジェクトを渡す（新規か更新か自動判定してくれる）
    - @user が 新規レコードなら POST /users（= users_path）
    - @user が 既存レコードなら PATCH /users/:id（= user_path(@user)）
    - 👉 Rails が自動でルーティングを判断してくれます。
  - url: … 送り先を直接指定する場合
  - scope: … パラメータの名前空間を指定（例：scope: :search → params[:search][:keyword]）
  - local: true … JS(AJAX)を使わず普通のHTMLフォームにする（初心者はまずこれ推奨）
* `form_with url: users_path, method: :post...`これはURLを直接指定するver
* **`url:`** を指定すると新規作成用フォーム
* **`model:`** を指定すると既存オブジェクトに基づくフォーム

  * `@user.new_record?` なら **新規作成**
  * `@user.persisted?` なら **更新**
* `local: true` を指定すると通常のフォーム送信（同期リクエスト）
* 指定しない場合は Rails 7 では **Turboによる非同期送信** になる

補足
- resources
  - 新規作成（コレクションに対する作成）→ users_path（複数形）
  - 既存の1件に対する参照/更新/削除 → user_path(@user)（単数形＋ID/オブジェクト必須）
- resource
  - 単数なので ID なしでアクセス：user_path（←この場合は引数不要）
  - 用途：1人1つしかないもの（例：自分のプロフィール設定）
---

## 演習問題

次のフォームを `form_with` で書いてください。

1. `/boards` に新規掲示板を作るフォーム（`title` と `body`）
2. 既存の `@board` を編集するフォーム（`local: true`）
3. コメント投稿フォーム（`@comment` モデルを使ってAjaxで送信）

---

## 答え合わせ

```erb
<!-- 1. 掲示板作成 -->
<%= form_with url: boards_path, local: true do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
  <%= f.label :body %>
  <%= f.text_area :body %>
  <%= f.submit "作成" %>
<% end %>

<!-- 2. 掲示板編集 -->
<%= form_with model: @board, local: true do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
  <%= f.label :body %>
  <%= f.text_area :body %>
  <%= f.submit "更新" %>
<% end %>

<!-- 3. コメント投稿 -->
<%= form_with model: @comment do |f| %>
  <%= f.label :body %>
  <%= f.text_area :body %>
  <%= f.submit "コメントする" %>
<% end %>
```

---

## 理解チェック（クイズ）

* Q1: `form_with model: @user` を使うと、Railsはどのように「新規」と「編集」を見分ける？
* Q2: Rails 7 で `form_with` を書くとデフォルトで同期送信？非同期送信？
* Q3: `local: true` を指定するとどうなる？

### 補足1
- form_with は 「新規か編集か」を自動判定してくれる
- その判定に persisted? を内部で利用している
- 結果として、POST（新規）か PATCH（編集）かが自動で切り替わる
```
form_with model: @user
        │
        └─> @user.persisted? == false → POST /users
        └─> @user.persisted? == true  → PATCH /users/:id

```
### 補足2
💡 イメージ：

1. ユーザーが「新規登録」画面（/users/new）を開く
2. 名前やメールを入力して「登録」ボタンを押す
3. その送信先が POST /users → UsersController#create
4. DBに保存されて、登録完了ページやマイページにリダイレクト

### 補足3
実際の流れ（編集のとき）

1. ユーザーが GET /users/:id/edit にアクセス（編集フォーム画面を開く）
2. フォームを入力して「更新」ボタンを押す
3. フォーム送信先は PATCH /users/:id
4. サーバー側の UsersController#update が呼ばれる
5. @user.update(user_params) が実行され、DBのデータが更新される
6. 更新が成功すれば詳細画面や一覧画面にリダイレクト
---

## まとめ

* `form_with` はフォーム生成の標準的なヘルパーメソッド
* **`url:` 指定 → 新規フォーム**
* **`model:` 指定 → モデルに応じて新規 or 編集フォーム**
* **Rails 7 では非同期がデフォルト**
* `local: true` を指定すれば従来通りの同期リクエスト

---
