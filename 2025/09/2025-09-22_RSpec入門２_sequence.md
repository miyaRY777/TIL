
FactoryBotの `sequence` は、**重複しない値を自動で作るための仕組み** です。
特に `email` のように一意制約（`uniqueness: true`）があるカラムでよく使います。

---

# ✅ `sequence` の使い方

---

## 1. 基本形

```ruby
sequence(:name) { |n| "user_#{n}" }
```

* `|n|` は「何番目の呼び出しか」を示すカウンター。
* 1回目なら `n = 1`、2回目なら `n = 2`。
* 値は `"user_1"`, `"user_2"`, `"user_3"` … と自動生成される。

---

## 2. 具体例：メールアドレスでの利用

```ruby
FactoryBot.define do
  factory :user do
    sequence(:email) { |n| "runteq_#{n}@example.com" }
    password { "password" }
  end
end
```

### どう動く？

```ruby
user1 = create(:user)
# => email: "runteq_1@example.com"

user2 = create(:user)
# => email: "runteq_2@example.com"

user3 = create(:user)
# => email: "runteq_3@example.com"
```

👉 自動で「被らないメールアドレス」が作られる！

---

## 3. なぜ必要？

* `User` モデルで `validates :email, uniqueness: true` を設定している場合、
  同じ `email` を2回作ろうとするとバリデーションエラーになる。
* `sequence` を使うとテストでユーザーを何人でも作れる。

---

## 4. 応用例

### 独自のフォーマット

```ruby
sequence(:username) { |n| "member-#{1000 + n}" }
# => member-1001, member-1002, member-1003 ...
```

### traitと組み合わせ

```ruby
factory :admin_user, parent: :user do
  sequence(:email) { |n| "admin_#{n}@example.com" }
  role { :admin }
end
```

---

## 🌟 まとめ

* **`sequence` は連番を自動で生成する機能**
* `|n|` が呼び出し回数に応じて増える
* 一意制約のあるカラム（`email`, `username` など）で必須級
* `sequence(:email) { |n| "runteq_#{n}@example.com" }` と書けば

  * 1回目 → `"runteq_1@example.com"`
  * 2回目 → `"runteq_2@example.com"`

---
