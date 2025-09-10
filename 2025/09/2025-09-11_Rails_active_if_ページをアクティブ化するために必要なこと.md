1. ヘルパーメソッドの定義
まず、app/helpers/application_helper.rbにactive_ifメソッドを定義します：

# app/helpers/application_helper.rb
```
module ApplicationHelper
  def active_if(path)
    path == controller_path ? 'active' : ''
  end
end
```

2. サイドバーでの実装
app/views/admin/shared/_sidebar.html.erbで各メニューリンクに適用します：
```
<nav class="navbar-nav">
  <li class="nav-item">
    <%= link_to admin_users_path, class: "nav-link #{active_if('admin/users')}" do %>
      <i class="bi bi-person"></i>
      ユーザー一覧
    <% end %>
  </li>
  <li class="nav-item">
    <%= link_to admin_boards_path, class: "nav-link #{active_if('admin/boards')}" do %>
      <i class="bi bi-file-earmark"></i>
      掲示板一覧
    <% end %>
  </li>
</nav>
```
3. controller_pathの正確な理解
各ページでのcontroller_pathの値を把握することが重要です：

ユーザー関連ページ
```
admin/users#index → controller_path = "admin/users"
admin/users#show → controller_path = "admin/users"
admin/users#edit → controller_path = "admin/users"
```
掲示板関連ページ
```
admin/boards#index → controller_path = "admin/boards"
admin/boards#show → controller_path = "admin/boards"
admin/boards#edit → controller_path = "admin/boards"
```
4. 実装時の重要ポイント
✅ 名前空間を含める
```
# ❌ 間違い
active_if('users')    # 名前空間が抜けている
active_if('boards')   # 名前空間が抜けている

# ✅ 正しい
active_if('admin/users')   # adminの名前空間を含める
active_if('admin/boards')  # adminの名前空間を含める
```
✅ CSSクラスの適切な結合
```
<!-- 基本のnav-linkクラスとactive_ifの結果を結合 -->
class: "nav-link #{active_if('admin/boards')}"
```
✅ HTMLの構造を正しく保つ
```
<nav>タグの開閉
<li>タグの開閉
<div>タグの開閉
```
5. 動作の仕組み
例：掲示板一覧ページ（admin/boards#index）にいる場合
```
controller_path = "admin/boards"
active_if('admin/boards') → "admin/boards" == "admin/boards" → true → 'active'
active_if('admin/users') → "admin/users" == "admin/boards" → false → ''
```
生成されるHTML:
```
<!-- 掲示板一覧メニュー（アクティブ） -->
<a href="/admin/boards" class="nav-link active">掲示板一覧</a>

<!-- ユーザー一覧メニュー（非アクティブ） -->
<a href="/admin/users" class="nav-link ">ユーザー一覧</a>
```
6. デバッグ方法
もしうまく動作しない場合：

controller_pathを確認
```
<!-- 一時的にビューに追加して確認 -->
<p>現在のcontroller_path: <%= controller_path %></p>
```
active_ifの戻り値を確認
```
<p>boards: "<%= active_if('admin/boards') %>"</p>
<p>users: "<%= active_if('admin/users') %>"</p>
```
ブラウザの開発者ツールで確認
```
生成されたHTMLのクラス名をチェック
.nav-link.activeの要素が存在するか確認
```
補足
1. controller_path の出力
```
<p>現在のcontroller_path: <%= controller_path %></p>
```
controller_path は Rails が内部的に持っている「今実行中のコントローラ名」を表します。
例えば Admin::BoardsController の場合は "admin/boards" が出力されます。
👉 このコードをページに埋め込むと「現在のcontroller_path: admin/boards」とブラウザに表示されます。

2. active_if の出力
```
<p>boards: "<%= active_if('admin/boards') %>"</p>
<p>users: "<%= active_if('admin/users') %>"</p>
```
active_if ヘルパーは、こういう実装になっているはずです：
```
def active_if(path)
  path == controller_path ? 'active' : ''
end
```
active_if('admin/boards')
→ 今の controller_path が "admin/boards" なら "active" を返す。違うなら ""（空文字）。
active_if('admin/users')
→ 今の controller_path が "admin/users" なら "active" を返す。違うなら空文字。

👉 つまり、これをブラウザに出すと例えばこうなります：
```
現在のcontroller_path: admin/boards
boards: "active"
users: ""
```
7. テストで確認される項目
今回のテストでは以下が確認されます：
```
# 掲示板関連ページで掲示板一覧メニューがアクティブ
expect(page).to have_css('.nav-link.active', text: '掲示板一覧')

# ユーザー関連ページでユーザー一覧メニューがアクティブ
expect(page).to have_css('.nav-link.active', text: 'ユーザー一覧')
```
### まとめ
ページをアクティブ化するために必要なのは：

ヘルパーメソッドの定義（application_helper.rb）
正確なcontroller_pathの指定（名前空間を含む）
適切なCSSクラスの結合
正しいHTML構造の維持
サーバーの再起動（ヘルパーメソッド変更後）

