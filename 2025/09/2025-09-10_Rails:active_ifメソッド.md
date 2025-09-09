# active_if メソッド
```
def active_if(path)
  path == controller_path ? 'active' : ''
end
```

### 何をしている？

- ナビゲーションのリンクを「今いるページならアクティブ表示」にするための判定。
- 引数 path と、現在の controller_path（Rails が持つ現在のコントローラ名）を比較。
  - 一致したら 'active' を返す
  - 一致しなければ空文字 '' を返す

### 処理の流れ
1. ビューで active_if('admin/boards') を呼ぶ
2. その 'admin/boards' が path に入る
3. controller_path が 'admin/boards' なら一致 → 'active' を返す
4. 結果として HTML は
```
<a class="nav-link active" href="/admin/boards">掲示板一覧</a>
```

補足
- path … 「このリンクがどのコントローラに属するか」を 人間（開発者）が指定する値
- controller_path … 「今実際に表示しているページのコントローラ」を Rails が教えてくれる値
