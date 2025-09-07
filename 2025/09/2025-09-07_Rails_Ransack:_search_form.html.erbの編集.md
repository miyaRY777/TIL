
```erb
<!-- app/views/admin/boards/_search_form.html.erb -->
```

👉 このファイルは **「掲示板 (boards) の検索フォーム」** を表示するためのビュー部分（HTMLを書く場所）です。
`_search_form.html.erb` のように **`_`から始まるファイル** は「部分テンプレート（部分的なビュー）」と呼ばれ、他の画面から読み込んで使います。

---

```erb
<%= search_form_for @q, url: admin_boards_path do |f| %>
```

👉 `search_form_for` は Ransack という検索用ライブラリが提供している特別なフォーム。

* `@q` … コントローラから渡された **検索条件を入れる箱**。
* `url: admin_boards_path` … 検索したときに送信先となるページ。今回は管理画面の「掲示板一覧」に検索条件を送ります。
* `do |f|` … この中に「検索フォームの中身」を書くよ、という意味。`f` はフォームの部品を作るための道具。

---

```erb
  <div class="row">
    <div class="d-flex align-items-center justify-content-center">
```

👉 HTML の見た目を整えるためのタグ。
Bootstrap という CSS フレームワークを使っていて、

* `row` … 横並びの行を作る。
* `d-flex align-items-center justify-content-center` … 部品を横並びにして、中央に寄せる。


---

```erb
      <div class="d-inline me-3">
        <%= f.search_field :user_first_name_or_user_last_name_cont, 
            class: 'form-control', 
            placeholder: 'ユーザー名' %>
      </div>
```

👉 **ユーザー名検索の入力欄**。

* `f.search_field` … 「検索用のテキスト入力」を作る。
* `:user_first_name_or_user_last_name_cont` … **名字または名前に、その文字が含まれているか** を検索する条件。
* `class: 'form-control'` … Bootstrap の見た目を適用して、きれいな入力フォームにする。
* `placeholder: 'ユーザー名'` … 入力欄の薄い文字で「ユーザー名」と表示。

---

```erb
      <!-- 掲示板のタイトル・本文検索 -->
      <div class="d-inline me-3">
        <%= f.search_field :title_or_body_cont, 
            class: 'form-control', 
            placeholder: t('defaults.search_word') %>
      </div>
```

👉 **掲示板のタイトルや本文を検索する入力欄**。

* `:title_or_body_cont` … 掲示板のタイトルか本文に文字が含まれているかを検索。
* `t('defaults.search_word')` … 多言語対応の仕組み（i18n）。翻訳ファイルから「検索ワード」という文字を呼び出している。

---

```erb
      <!-- 作成日の範囲指定 -->
      <div class="d-flex me-3">
        <%= f.date_field :created_at_gteq, class: 'form-control' %>
        <span class="mx-1">〜</span>
        <%= f.date_field :created_at_lteq_end_of_day, class: 'form-control' %>
      </div>
```

👉 **作成日（掲示板が作られた日）で検索する範囲を指定する部分**。

* `f.date_field :created_at_gteq` … 何日から（開始日）。
* `f.date_field :created_at_lteq_end_of_day` … 何日まで（終了日）。
* `〜` … 人間が見てわかりやすいように「から〜まで」を区切る記号。

---

```erb
      <%= f.submit class: 'btn btn-primary' %>
```

👉 「検索する」ボタン。

* `f.submit` … フォームの内容を送信するボタン。
* `class: 'btn btn-primary'` … 青色のボタンにする（Bootstrapのデザイン）。

---

```erb
    </div>
  </div>
<% end %>
```

👉 `</div>` … 開いていた `<div>` を閉じているだけ。
👉 `<% end %>` … 最初に書いた `do |f|` の終わり。フォームの中身はここまで。

---

💡 まとめ
このコードは「掲示板の検索フォーム」で、

* ユーザー名
* 掲示板のタイトル/本文
* 作成日の範囲

を入力して「検索」できる仕組みです。

---

