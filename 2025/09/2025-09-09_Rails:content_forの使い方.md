# content_for
「情報を一時的に保存して、後で別の場所で使う」ためのメソッド

- ビューファイルで content_for を使って情報を「預ける」
- レイアウトファイルで yield を使ってその情報を「受け取る」

### レイアウトファイルの準備
```
<!-- app/views/layouts/application.html.erb -->
<html>
<head>
  <title><%= yield(:title) %></title>
</head>
<body>
  <%= yield %>
</body>
</html>
```
- <%= yield(:title) %> → ビューで content_for(:title, ...) した内容をここに表示。
（ブラウザのタブタイトル用）

- <%= yield %> → コントローラで呼ばれたビューの「本文全体」をここに表示。

### 各ビューファイルでの使用
```
<!-- app/views/boards/show.html.erb -->
<% content_for(:title, @board.title) %>

<h1><%= @board.title %></h1>
<!-- 掲示板の内容 -->
```
- <% content_for(:title, @board.title) %> → このページ用のタイトルを「title」領域にセット。
- <h1><%= @board.title %></h1> → ページ内の見出しとして表示。

なるほど！タイトルをビューで指定しなかったときに「デフォルトタイトル」を表示したいんですね 👍

Rails では `||` を使って簡単に書けます。

---

### レイアウト側 (`application.html.erb`)

```erb
<html>
<head>
  <title><%= yield(:title) || "デフォルトタイトル" %></title>
</head>
<body>
  <%= yield %>
</body>
</html>
```

* `yield(:title)` に値があればそれを表示。
* なければ `"デフォルトタイトル"` を表示。

---

### ビュー側（例：`boards/show.html.erb`）

```erb
<% content_for(:title, @board.title) %>

<h1><%= @board.title %></h1>
<p>掲示板の内容</p>
```

この場合は `@board.title` が `<title>` に入ります。

---

### もし `content_for` を書かなかった場合（例：`boards/index.html.erb`）

```erb
<h1>掲示板一覧</h1>
<p>一覧ページの内容</p>
```

このページは `<% content_for(:title, ...) %>` がないので、
レイアウト側の `|| "デフォルトタイトル"` が効いて `"デフォルトタイトル"` が `<title>` に表示されます。

---

👉 これで「設定がなければデフォルト」を安全に扱えます。

---
