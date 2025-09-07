
---

## 1. 全体の構造

```erb
<div class="container mb-5 pt-2">
  <div class="row">
    <div class="col-md-12">
      ...
    </div>
  </div>
</div>
```

* **`container`**: ページ全体を中央に配置するための枠（Bootstrapのクラス）。
* **`row`**: 行の単位。Bootstrapのグリッドシステムでレイアウトを整えます。
* **`col-md-12`**: 1行を12分割したうち全て使う、つまり**横幅いっぱい**。

---

## 2. ページタイトル

```erb
<h1><%= t('.title') %></h1>
```

* `<h1>`: ページの見出しタグ。
* `<%= t('.title') %>`: Railsの**i18n（国際化）機能**で翻訳文を表示。

  * 例えば、`config/locales/ja.yml`で

    ```yaml
    ja:
      admin:
        boards:
          index:
            title: "掲示板一覧"
    ```

    と書くと、このページでは「掲示板一覧」と表示されます。

---

## 3. 検索フォーム

```erb
<%= render 'search_form' %>
```

* `render 'search_form'` は**部分テンプレート**を呼び出す指示。
* `_search_form.html.erb` というファイルに検索フォームをまとめておくと、ここで表示されます。
* これにより、コードが整理され、再利用しやすくなります。

---

## 4. 掲示板一覧テーブル

```erb
<div class="table-responsive">
  <table class="table table-striped">
```

* **`table-responsive`**: 小さい画面でも横スクロールしてテーブルを表示可能に。
* **`table table-striped`**: Bootstrapのテーブルクラスで、行ごとに色が交互になる（見やすい）。

---

### ヘッダー部分

```erb
<thead>
  <tr>
    <th><%= Board.human_attribute_name(:id) %></th>
    <th><%= Board.human_attribute_name(:title) %></th>
    <th><%= Board.human_attribute_name(:user) %></th>
    <th><%= Board.human_attribute_name(:created_at) %></th>
    <th><%= t('defaults.action') %></th>
  </tr>
</thead>
```

* **`Board.human_attribute_name(:id)`**
  → データベースのカラム名を**人間向けの文字列**に変換して表示
  → 例: `id` → 「ID」
* `t('defaults.action')` → 「操作」と表示されます。
* テーブルの列は **ID、タイトル、投稿者、作成日、操作** です。

---

### データ部分（一覧表示）

```erb
<tbody>
  <% @boards.each do |board| %>
    <tr>
      <td><%= board.id %></td>
      <td><%= link_to board.title, admin_board_path(board) %></td>
      <td><%= board.user.full_name %></td>
      <td><%= l board.created_at, format: :short %></td>
      <td>
        <%= link_to t('defaults.show'), admin_board_path(board), class: 'btn btn-info btn-sm me-1' %>
        <%= link_to t('defaults.edit'), edit_admin_board_path(board), class: 'btn btn-warning btn-sm me-1' %>
        <%= link_to t('defaults.delete'), admin_board_path(board), method: :delete,
            class: 'btn btn-danger btn-sm',
            data: { confirm: t('defaults.delete_confirm') } %>
      </td>
    </tr>
  <% end %>
</tbody>
```

#### ポイント解説

1. `@boards.each do |board|`

   * コントローラから渡された**掲示板の配列**を一つずつ取り出して表示します。

2. `<%= board.id %>` → 掲示板のIDを表示

3. `<%= link_to board.title, admin_board_path(board) %>`
   → タイトルをクリックすると**詳細ページ**に飛ぶリンク

4. `<%= board.user.full_name %>`
   → 投稿者名を表示。Userモデルに `full_name` メソッドがある前提。

5. `<%= l board.created_at, format: :short %>`
   → 日付を短く表示（例: 2025/09/08）
   
  5:補足
  - l:l は localize（ローカライズ） の略
    - 日付や時刻をユーザーに見やすい形式に変換するために使います。
    - Railsでは、config/locales/ja.yml にフォーマットの定義ができます

  - format: formatで表示形式を指定できます。
    - short: 月日と時間だけを表示する「短い形式」のこと
    ```
    ja:
      time:
        formats:
          short: "%m/%d %H:%M"
    ```
  

6. 操作ボタン

   * **show** → 詳細ページ
   * **edit** → 編集ページ
   * **delete** → 削除。`method: :delete` でDELETEリクエストを送る
   * `data: { confirm: ... }` → 削除前に確認ダイアログ

---

## 5. ページネーション

```erb
<%= paginate @boards if respond_to?(:paginate) %>
```

* `@boards` が **kaminariやwill_paginateでページ分割**されている場合にページリンクを表示。
* `respond_to?(:paginate)` は、もしpaginateが使えない場合（未設定）には表示しないようにしています。

---

## 6. まとめ

このビューの役割は：

1. ページタイトルを表示
2. 検索フォームを表示
3. 掲示板一覧を表形式で表示
4. 各行に「詳細」「編集」「削除」ボタンを表示
5. ページネーションを表示

**ポイント**：

* Railsでは **`@変数` にデータを入れ、ビューでループして表示** する
* **i18n（tメソッド）** で文字列を管理
* **Bootstrap** で見た目を整える
* **CRUD操作（詳細・編集・削除）** が1つのテーブルで管理可能

---

💡 補足：

* `board.user.full_name` がエラーになる場合は、Userモデルに次を追加してください：

```ruby
class User < ApplicationRecord
  def full_name
    "#{first_name} #{last_name}"
  end
end
```

---
