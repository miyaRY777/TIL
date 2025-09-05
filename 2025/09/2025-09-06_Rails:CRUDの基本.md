# 表示系アクション（GET）

## indexアクション（一覧表示）
```
def index
  @boards = Board.all  # 複数形で全ての掲示板を取得
end
```
解説
何をしている？
データベースにあるすべての掲示板（Boardモデルのデータ）を取り出して、@boardsに入れている。

なぜ必要？
「掲示板一覧ページ」を表示するため。ビューで@boards.eachのようにして全件表示できる。

## showアクション（詳細表示）
```
def show
  @board = Board.find(params[:id])  # 単数形で特定の掲示板を取得
end
```


## newアクション（新規作成フォーム）
```
def new
  @board = Board.new  # 新しい空のインスタンスを作成
end
```
解説
何をしている？
空っぽの掲示板インスタンスを作成して@boardに入れている。

なぜ必要？
「新規作成フォーム」でform_with model: @boardを書くと、この空のインスタンスを使ってフォームが生成される。

## editアクション（編集フォーム）
```
def edit
  @board = Board.find(params[:id])  # 編集対象の掲示板を取得
end
```

# 処理系アクション

## createアクション（新規作成処理）
```
def create
  @board = Board.new(board_params)  # パラメータから新しい掲示板を作成
  
  if @board.save  # 保存に成功した場合
    redirect_to boards_path, success: '掲示板が作成されました'
  else  # 保存に失敗した場合
    flash.now[:danger] = '掲示板の作成に失敗しました'
    render :new, status: :unprocessable_entity  # newテンプレートを再表示
  end
end
```
解説
- ここで使っている board_params は、private メソッドで定義されているもの
- status: :unprocessable_entity は「入力が正しくなかったので保存できませんでした」ということを、ユーザーとブラウザに伝えるための設定

## updateアクション（更新処理）
```
def update
  @board = Board.find(params[:id])  # 更新対象の掲示板を取得
  
  if @board.update(board_params)  # 更新に成功した場合
    redirect_to board_path(@board), success: '掲示板が更新されました'
  else  # 更新に失敗した場合
    flash.now[:danger] = '掲示板の更新に失敗しました'
    render :edit, status: :unprocessable_entity  # editテンプレートを再表示
  end
end
```

## destroyアクション（削除処理）
```
def destroy
  @board = Board.find(params[:id])  # 削除対象の掲示板を取得
  @board.destroy!  # 削除実行
  redirect_to boards_path, success: '掲示板が削除されました', status: :see_other
end
```

# privateメソッド
```
private

def board_params
  params.require(:board).permit(:title, :body, :board_image)
end
```
解説
params
- フォームから送られてきた入力データが全部入っているもの。

require(:board)
- 送られてきたデータの中から、:board というキーを必ず含んでいることを保証します。
- つまり params[:board] を取り出すイメージ。

permit(:title, :body, :board_image)
- セキュリティのため「許可するカラムだけ」を指定します。
- → これをやらないと、ユーザーが勝手に「管理者フラグ」などを送信してアプリを不正操作できてしまう可能性があるんです。

✅ まとめると
board_params は 「フォームから送られてきた安全なデータだけを取り出す」仕組み です。

### privateメソッドとは？
1. そもそも「メソッド」とは？

メソッド = 「処理をまとめた箱」みたいなものです。
例えば board_params も「安全なデータを取り出す処理」をひとまとめにしたメソッドです。

2. private とは？

Rubyでは、クラスの中で宣言したメソッドを「外から呼び出せるかどうか」を決めるキーワードがあります。

public → 外から自由に呼び出せる（デフォルト）
private → 同じクラスの中からしか呼び出せない
protected → ちょっと特殊（今回は省略）

# 重要なポイント
1. 変数名の使い分け
複数形 → @boards（一覧で複数のデータ）
単数形 → @board（1つのデータ）

2. 成功時と失敗時の処理
成功時 → redirect_to（別のページへ移動）
失敗時 → render（同じページを再表示、エラー情報を保持）

3. HTTPステータスコード
status: :unprocessable_entity（422エラー）
status: :see_other（303リダイレクト）

4. フラッシュメッセージ
success: → 成功メッセージ
flash.now[:danger] → エラーメッセージ（renderの場合）
