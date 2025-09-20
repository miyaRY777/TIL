```
Validation failed: Password confirmation can't be blank
```
意味
- パスワード確認が空白です」という意味
解決
- Userモデルにはpasswordだけでなく、もう一つ必要な属性が必要
- password_confirmationという属性が必要
修正したコード
```ruby
user = User.create!(email: "user@example.com", password: "password", password_confirmation:"password")
```