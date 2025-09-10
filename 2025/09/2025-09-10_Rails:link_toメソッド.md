
# Railsメソッド学習メモ: `link_to`

## 問題

Railsでリンクを作りたいです。
次の3つを `link_to` を使って実装してください。

1. Googleへのリンクを作成
2. `/users` に飛ぶ「ユーザー一覧」リンクを作成
3. 特定のユーザーを削除する「削除」リンクを作成（確認ダイアログ付き）

---

## 回答例
```erb
<%= link_to "Google", "https://www.google.com" %>
<%= link_to "ユーザー一覧", users_path %>
<%= link_to "削除", user_path(@user), method: :delete, data: { confirm: "本当に削除しますか？" } %>
```


---

## 解説

* **第1引数** … リンクの表示文字（画面に出る文字列）
* **第2引数** … 遷移先のURLやパス（`users_path` などのパスヘルパーが便利）
* **オプション（第3引数以降）** … `class`, `id`, `method`, `data` などを指定可能

### Rails 7 の注意点

* Rails 7 では Turbo が導入されているため、`method: :delete` を使うときは `turbo-rails` が必要。
* `application.js` に以下があることを確認する：

  ```js
  import "@hotwired/turbo-rails"
  ```

---

## 演習問題

次のリンクを `link_to` で書いてください。

1. `/boards` に飛ぶ「掲示板一覧」リンク
2. `/users/1/edit` に飛ぶ「ユーザー編集」リンク（Bootstrapの `btn btn-warning` クラス付き）
3. `/boards/2` を削除する「掲示板削除」リンク（確認ダイアログ付き）

---

## 答え合わせ

```erb
<%= link_to "掲示板一覧", boards_path %>
<%= link_to "ユーザー編集", edit_user_path(1), class: "btn btn-warning" %>
<%= link_to "掲示板削除", board_path(2), method: :delete, data: { confirm: "本当に削除しますか？" } %>
```

---

## 理解チェック（クイズ）

* Q1: `link_to` の第1引数は何を表す？
* Q2: `link_to "削除", user_path(@user), method: :delete` が普通の `<a>` と違うのはなぜ？
* Q3: Rails 7 で `method: :delete` が動作するために必要な仕組みは？

---
## 答え

## Q1: `link_to` の第1引数は何を表す？

`link_to` の書き方には2パターンあります。

### (1) 文字列を渡した場合

```erb
<%= link_to "ユーザー一覧", users_path %>
```

* **第1引数 `"ユーザー一覧"`** … リンクに表示される文字列（リンクテキスト）
* **第2引数 `users_path`** … リンク先のURL

出力結果：

```html
<a href="/users">ユーザー一覧</a>
```

### (2) ブロックを渡した場合

```erb
<%= link_to users_path do %>
  <i class="bi bi-person"></i> ユーザー一覧
<% end %>
```

* **第1引数 `users_path`** … リンク先のURL
* ブロックの中身 … リンクに表示するHTML要素

出力結果：

```html
<a href="/users"><i class="bi bi-person"></i> ユーザー一覧</a>
```
つまり「第1引数」は、**文字列の場合はリンクテキスト**、**ブロックを使う場合はリンク先URL**を表します。

---

## Q2: `link_to "削除", user_path(@user), method: :delete` が普通の `<a>` と違うのはなぜ？

通常の `<a>` タグは **GET リクエストしか送れません**。
でも Rails の `link_to` には `method:` オプションがあり、

```erb
<%= link_to "削除", user_path(@user), method: :delete %>
```

と書くと、**HTTP DELETE リクエスト** を送るリンクになります。

生成されるHTMLはこんな感じです：

```html
<a data-method="delete" href="/users/1" rel="nofollow">削除</a>
```

👉 見た目はただの `<a>` ですが、`data-method="delete"` が付与されていて、Rails 側の仕組みがこれを検知して **フォームを動的に作って送信**してくれます。

---

## Q3: Rails 7 で `method: :delete` が動作するために必要な仕組みは？

Rails 7 では **Rails UJS が廃止**されました。
その代わりに **Turbo + rails-ujs 互換の仕組み**（正確には *Turbo + @rails/request.js*）が必要です。

### 必要な仕組み

* **importmap を使っている場合**

  ```js
  // app/javascript/application.js
  import "@hotwired/turbo-rails"
  import Rails from "@rails/ujs"
  Rails.start()
  ```
* **Webpacker / esbuild / Vite を使っている場合**も同様に `@rails/ujs` を import して start する必要があります。

これで、`data-method="delete"` が有効になり、リンククリック時に **JavaScript が裏で DELETE リクエストを送信**してくれます。

👉 要するに、**Rails 7 で `method: :delete` を使うためには、JavaScript側で UJS（Unobtrusive JavaScript）が動作している必要がある**ということです。

---

## まとめ

* `link_to` は Rails のビューでリンクを生成する便利メソッド
* 基本: `link_to "表示文字", URL`
* パスヘルパーを使えばルート変更に強い
* オプションで `class`, `method`, `data` などを柔軟に指定可能
* Rails 7 では Turbo が支える

---