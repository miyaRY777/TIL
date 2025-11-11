
# FactoryBotとは何か？

<details>
1. テスト用のデータを簡単に作るためのgem<br>
2. Railsに標準であるFixtureの代わりとして使われる
</details>

# なぜ必要なの？

<details>
大前提として、Railsでテストを書く時に、ユーザーや記事などのデータを用意しなければならない<br>
ただし、次のようなコードを毎回用意するのは大変である<br>
user = User.create(email: "test@example.com", password: "password")<br>
<br>
→FactoryBotを使うと、テンプレのようにデータを自動生成できる
</details>

# 実用例
#### factoryの定義ファイル
```rb
FactoryBot.difine do
	factory :user do
		sequence(:email) {|n| "user#{n}@example.com"}
		password {"password"}
	end
end
```


#### テストコードで呼び出す
```ruby
# FactoryBotのuserを生成
user = FactoryBot.create(:user)

# buildはDBに保存しない
user = FactoryBot.build(:user)

```

##### 解説
- `create(:user)` → データベースに保存される
		実際にDBに保存されるので
		- バリデーションが走る
	    - 関連（アソシエーション）の整合性も確認できる
	      
- `build(:user)` → 保存せずにオブジェクトだけ作る
	  - **DBにアクセスしないので高速**
	  - 必要最低限のオブジェクトだけ作れる（軽量）
	  - 単体テストで「バリデーション通るかだけ確認したい」時に便利