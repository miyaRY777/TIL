了解しました！
`render` と `redirect_to` の違いを、初学者でも実際に手を動かしながら理解できるように **学習ノート形式** にまとめました。
以下をそのまま GitHub の学習メモに貼り付けられます。

---

# Railsメソッド解説：`render` と `redirect_to`

## 問題

Railsにはビューを表示する方法として **`render`** と **`redirect_to`** があります。
この2つはどのように違うのでしょうか？

---

## 回答例

* `render` : **同じリクエストの中で指定したビューを表示する**
* `redirect_to` : **ブラウザに「別のURLへ移動せよ」と指示し、新しいリクエストを送らせる**

---

## 解説

### 🔹 `render`

* コントローラ内で `render` を呼び出すと、**対応するビューをそのまま表示**します。
* リクエストは**1回だけ**で完結します。
* URLは変わりません。

例：

```ruby
class ArticlesController < ApplicationController
  def show
    @article = Article.find(params[:id])
    # 明示的に指定すると「app/views/articles/show.html.erb」を表示
    render :show
  end
end
```

➡ ブラウザのURLは `/articles/1` のまま。
➡ 表示するHTMLだけを切り替えるイメージ。

---

### 🔹 `redirect_to`

* `redirect_to` はブラウザに **「別のURLにアクセスしてください」** と指示します。
* その結果、ブラウザが **新しいリクエストを送る** ので処理が2回発生します。
* URLは **リダイレクト先に変わる** のが特徴です。

例：

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(article_params)
    if @article.save
      redirect_to @article, notice: "記事を作成しました"
    else
      render :new, status: :unprocessable_entity
    end
  end
end
```

➡ 成功時：ブラウザが `/articles/:id` に移動し、再リクエストする。
➡ 失敗時：URLは `/articles` のまま、`new.html.erb` を表示する。

---

## 演習問題

1. コントローラで記事の作成に失敗したときに `render` を使う理由は？
2. `redirect_to` を使ったとき、ブラウザ側で何が起きる？
3. `render` と `redirect_to` の違いを自分の言葉で説明してください。

---

## 答え合わせ

1. **失敗したときに入力内容を保持したままフォームを再表示するため**。

   * `redirect_to` だとリクエストがリセットされ、入力内容が消えてしまう。

2. **サーバーから「別の場所に行け」と返され、ブラウザが自動で新しいリクエストを送る**。

3. 例：

   * `render` → その場でビューを切り替えるだけ（URLは変わらない）。
   * `redirect_to` → ブラウザが新しいリクエストを送る（URLが変わる）。

---

## 理解チェック（クイズ）

**Q:**
次のコードが実行されたとき、URLバーの表示はどうなるでしょうか？

```ruby
class UsersController < ApplicationController
  def update
    if @user.update(user_params)
      redirect_to users_path
    else
      render :edit
    end
  end
end
```

* A. 成功時は `/users` に変わり、失敗時は `/users/:id/edit` のまま。
* B. 成功時も失敗時も `/users` に変わる。
* C. 成功時も失敗時も `/users/:id/edit` のまま。

---

## まとめ

* `render` : 同じリクエストでビューを表示。URLは変わらない。
* `redirect_to` : 別URLに移動させる。ブラウザが新しいリクエストを送る。
* フォーム送信の成否で `render` と `redirect_to` を使い分けるのが基本パターン。

---
