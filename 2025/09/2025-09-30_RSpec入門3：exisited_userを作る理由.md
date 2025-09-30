```ruby
require 'rails_helper'

RSpec.describe 'Users', type: :system do|
	let(:user) { create(:user) }
	describe 'ログイン前' do
		describe 'ユーザー新規登録' do
			context '登録済のメールアドレスを使用' do
				it 'ユーザーの新規作成が失敗する' do
					existed_user = create(:user)
					visit new_user_path
					fill_in 'Email', with: existed_user.email
					fill_in 'Password', with: 'password'
					fill_in 'Password confirmation', with: 'password'
					click_button 'SignUp'
					expect(page).to have_content '1 error prohibited this user from being saved'
					expect(page).to have_content 'Email has already been taken'
					expect(current_path).to eq users_path
					expect(page).to have_field 'Email', with: existed_user.email
				end
			end
		end
	end
end
```

解説

---
---
### 1. let(:user) { create(:user) }

- `let(:user)` は **テストで呼ばれたときに初めて実行**される遅延評価です。
- この場合 `create(:user)` により、`users` テーブルに1人ユーザーを作ります。
    
- ただし **そのテストの中で `user` を参照しない限り DB に作られません**。
    

---

### 2. existed_user = create(:user)

- これは `let` ではなく、テストの中で**即座に実行される**処理です。
- この行が実行された時点で `users` テーブルに1人ユーザーが作成され、`existed_user` という変数に入ります。
    

---

### 3. 同じかどうか？

- **違います**。
    
- `user` は `let(:user)` で定義されていますが、このテストでは一度も `user` を呼んでいないので、**実際にはまだ生成されていません**
- なので、この時点では DB に存在するのは `existed_user` だけです。
    

---

## 実際に確認

もし試しにテストの中で:
```ruby
p User.count
p user.id
p existed_user.id
```

結果
- `User.count` → 2（`user` を呼んだ時点で2人目が作成される）
- `user.id` → 2（新しく作られた人）
- `existed_user.id` → 1（先に作った人）

---
---
