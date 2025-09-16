## セットアップのために作業ブランチを作成

```
git checkout master
git branch
git checkout -b setup
git branch
```

## Dockerコンテナを立ち上げる
```
$ docker compose build
$ docker compose up
```
## Gemのインストール
```
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end

$ docker compose exec web bundle install
```

## 設定ファイルの作成

```
$ docker compose exec web bundle exec rails g rspec:install
```
- docker compose exec web → Rails のコンテナ内で実行
- bundle exec → Gemfile に書かれた正しいバージョンの gem を使う
- rails g → ジェネレーターを実行する
- rspec:install → RSpec 用の設定ファイルを生成する

## テストファイルの作成
```
$ docker compose exec web bundle exec rails generate rspec:model Task
```

## テストの実行
```
$ docker compose exec web bundle exec rspec spec/models/task_spec.rb
```

## 正常に実行されたことを確認して、この時点での修正内容をcommitして、GitHubにpush

補足
この `Gemfile` の記述を一行ずつ解説

---

## コード全体

```ruby
group :development, :test do
  gem 'rspec-rails'
  gem 'factory_bot_rails'
end
```

---

## 1. `group :development, :test do ... end`

* **意味**: Gem を使う環境（グループ）を限定する。
* Rails では環境ごとに `development`（開発環境）、`test`（テスト環境）、`production`（本番環境）があります。
* この書き方をすると、**開発環境とテスト環境でのみ読み込まれる Gem** になります。

  * → 本番環境には不要なツール（RSpec, FactoryBotなど）は入らないようにする工夫。

---

## 2. `gem 'rspec-rails'`

* **意味**: RSpec を Rails に統合して使えるようにする Gem。
* Rails 専用の設定やジェネレーターが使えるようになる。
* 例:

  ```bash
  rails g rspec:install
  ```

  で RSpec の設定ファイルを自動生成できるようになる。
* Railsの `model`, `controller` などを作るときに自動で `spec/` 以下にテストファイルも作られるようになる。

---

## 3. `gem 'factory_bot_rails'`

* **意味**: FactoryBot を Rails に統合して使える Gem。
* FactoryBot は **テストデータを簡単に作るためのライブラリ**。
* 例: ユーザーを作るときに

  ```ruby
  User.create!(name: "Taro", email: "taro@example.com")
  ```

  と毎回書く代わりに、

  ```ruby
  FactoryBot.create(:user)
  ```

  でサッとダミーデータを用意できる。
* 開発環境でも便利（動作確認用にテストデータを入れるなど）。

---

## まとめ

* **`group :development, :test`**
  → 開発とテストでしか使わない Gem をまとめる。
* **`rspec-rails`**
  → Rails で RSpec を使えるようにする。
* **`factory_bot_rails`**
  → テストデータを簡単に作れる FactoryBot を Rails と連携。

👉 つまり、このブロックは「開発・テスト環境でテストを書くためのツールを導入する設定」です。

---
