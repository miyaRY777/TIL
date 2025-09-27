#Rails #ActiveREcord

### **DB操作の自動化**  
ユーザーの入力とコントローラーの指示によって、SQLを書かずにデータ取得・保存をやってくれる
  
具体例
Controller から `User.find(params[:id])` などの命令を受けると、ActiveRecordがSQLに翻訳してDBからデータを取得します。

また `user.save` のようにすれば、フォーム入力内容をDBにINSERT/UPDATEしてくれる

👉 つまり「アプリとDBの橋渡し役」です。   
```ruby
@user = User.find(params[:id])   # DBからデータを取得
@user.update(name: "Taro")       # DBのデータを更新
```

### **モデルのバリデーション** と **関連付けの管理**  
- `validates :name, presence: true` など、データの正しさを守る
  
- `has_many`, `belongs_to` で他テーブルと繋がる  
	- → 多段階のリレーションも追える
	```ruby
	user = User.find(1)
    user.tasks # => 自動で tasks テーブルを見に行ってくれる
    
    # User has_many :tasks
    # Task belongs_to :project
    user = User.first
    user.tasks.first.project # => User → Task → Project と辿れる
	```


### バリデーション（データの正しさチェック）

- **役割**: DBに保存する前に「正しいデータか」をチェックする
- **ポイント**:
    - 「空欄ではダメ」 (`presence: true`)
    - 「文字数は○文字以内」 (`length`)
    - 「メールアドレス形式」 (`format`)
    
- **例**:
  ```ruby
    class User < ApplicationRecord
    validates :name, presence: true, length: { maximum: 20 }
    end
    
    ```

👉 これがあることで「正しくないデータがDBに入る前に防ぐ」ことができる。  
UX的にも「エラー文を返す」ことができるのでユーザーに優しい。

---


### **便利メソッドの提供**  
`where`, `order`, `joins`, `includes` などを組み合わせて複雑な検索も可能  

---
