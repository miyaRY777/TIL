## FactoryBotの流れ
1. Gemを導入する
```ruby
# Gemfile
group :development, :test do
  gem 'factory_bot_rails'
end
```
2. 初期設定
```rb
# spec/rails_helper.rb または spec/support/factory_bot.rb
RSpec.configure do |config|
  config.include FactoryBot::Syntax::Methods
end
```

3. Factoryファイルを作成する
4. テストで使う