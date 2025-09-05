# 概要

```ruby
<li class="<%= active_if('users') %>">
```

そして、ヘルパーメソッドはこうです：
```ruby
module ApplicationHelper
  def active_if(path)
    path == controller_path ? 'active' : ''
  end
end
```

では順番に説明します。

---

## 1. 「ヘルパーメソッド」って何？

Railsでは **ビュー（HTML部分）で使える便利な小さな関数** を「ヘルパーメソッド」と呼びます。
`ApplicationHelper` に書くと、アプリ全体で使えます。

---

## 2. `active_if` メソッドの意味

```ruby
def active_if(path)
  path == controller_path ? 'active' : ''
end
```

1. `path` という引数を受け取る
   → この場合 `'users'` のような文字列を渡しています。

2. `controller_path`
   → 現在表示しているページのコントローラー名を文字列で返します。
   例えば `UsersController` のページなら `'users'` が返ります。

3. 三項演算子 `? :` を使っている

   ```ruby
   条件 ? 真の場合 : 偽の場合
   ```

   → `path == controller_path` が真なら `'active'`、偽なら空文字 `''` を返します。

補足
- usersは、「このリスト項目はUsersControllerのページに対応している」という印」 です。

- UsersController:/users:ユーザー一覧ページやユーザー操作
- PostsController:/posts:投稿一覧ページや投稿操作
  - controller_path は今見ているページのコントローラー名を 'users' や 'posts' として返します。
---

## 3. ビューでの使い方

```erb
<li class="<%= active_if('users') %>">ユーザー一覧</li>
```

* `'users'` が今のページのコントローラー名と一致したら
  → `<li class="active">ユーザー一覧</li>` になる
  → CSSで「今開いているページ」というスタイルを変えられる
* 一致しなければ
  → `<li class="">ユーザー一覧</li>` になる
  → 特別なスタイルは適用されない

---

## 4. まとめ（未経験者向けポイント）

* `active_if` は「今のページならクラスに `active` を付けるかどうか」を判定するメソッド
* `controller_path` は「今開いているページのコントローラー名」を取得する
* 三項演算子で条件によって文字列を返す
* ビューの `class` に使うと、ナビゲーションメニューで「現在のページを強調表示」できる

---

💡 例：

```erb
<ul>
  <li class="<%= active_if('home') %>">ホーム</li>
  <li class="<%= active_if('users') %>">ユーザー一覧</li>
</ul>
```

* ホームページにいるとき → `<li class="active">ホーム</li>`
* ユーザー一覧ページにいるとき → `<li class="active">ユーザー一覧</li>`

これで「今開いているページ」がわかるメニューが作れます。

---
