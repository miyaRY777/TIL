
set_userメソッドとは？

基本的な仕組み
```
class Admin::UsersController < ApplicationController
  before_action :set_user, only: [:show, :edit, :update, :destroy]

  def show
    # @user = User.find(params[:id]) ← これを書く必要がない！
  end

  def edit
    # @user = User.find(params[:id]) ← これも書く必要がない！
  end

  def update
    # @user = User.find(params[:id]) ← これも書く必要がない！
  end

  def destroy
    # @user = User.find(params[:id]) ← これも書く必要がない！
  end

  private

  def set_user
    @user = User.find(params[:id])  # ここで一度だけ定義！
  end
end
```

なぜset_userが必要なのか？

❌ set_userを使わない場合（重複コード）
```
def show
  @user = User.find(params[:id])  # 同じコードを4回書く
end

def edit
  @user = User.find(params[:id])  # 同じコードを4回書く
end

def update
  @user = User.find(params[:id])  # 同じコードを4回書く
end

def destroy
  @user = User.find(params[:id])  # 同じコードを4回書く
end
```

✅ set_userを使った場合（DRY原則）
```
before_action :set_user, only: [:show, :edit, :update, :destroy]

# 各アクションは空でOK！before_actionで@userが自動的に設定される
def show
end

def edit
end

def update
  if @user.update(user_params)  # @userが既に設定済み
    # 成功処理
  end
end

def destroy
  @user.destroy!  # @userが既に設定済み
end
```

before_actionの動作順序
リクエストの流れ
ルーティング → GET /admin/users/1 → admin/users#show
before_action実行 → set_userメソッドが実行される
@user = User.find(params[:id]) → ID=1のユーザーを取得
showアクション実行 → @userが既に設定済み
ビュー表示 → @userを使ってHTMLを生成



実際のコード例
```
class Admin::UsersController < ApplicationController
  before_action :set_user, only: [:show, :edit, :update, :destroy]

  def index
    @users = User.all  # ここはset_userは実行されない
  end

  def show
    # @userは既にset_userで設定済み
  end

  def new
    @user = User.new  # ここはset_userは実行されない（新規作成なのでIDがない）
  end

  def create
    @user = User.new(user_params)  # ここもset_userは実行されない
    # 処理...
  end

  def edit
    # @userは既にset_userで設定済み
  end

  def update
    # @userは既にset_userで設定済み
    if @user.update(user_params)
      redirect_to admin_user_path(@user)
    else
      render :edit
    end
  end

  def destroy
    # @userは既にset_userで設定済み
    @user.destroy!
    redirect_to admin_users_path
  end

  private

  def set_user
    @user = User.find(params[:id])
  end

  def user_params
    params.require(:user).permit(:email, :first_name, :last_name, :avatar)
  end
end
```

メリット
1. DRY原則（Don't Repeat Yourself）
同じコードを何度も書かない
メンテナンスが楽

2. エラー処理の一元化
```
def set_user
  @user = User.find(params[:id])
rescue ActiveRecord::RecordNotFound
  redirect_to admin_users_path, alert: 'ユーザーが見つかりません'
end
```

3. 可読性の向上
各アクションの本質的な処理に集中できる
コードがスッキリする
復習との関連
