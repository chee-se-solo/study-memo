# Rails 単体テスト導入

単体テストを自動化。rubocop によるスタイルチェックも同時に行う。

## テスト系 Gem 導入

```ruby
group :development do
  # lint for ruby
  gem 'rubocop-performance', require: false # パフォーマンスに問題のある記述を検出
  gem 'rubocop-rails', require: false # rubocop 本体
  gem 'rubocop-rspec', require: false # RSpec 用 rubocop
  gem 'slim_lint', require: false # slim 用 lint
  gem 'overcommit', require: false # コミット、PUSH前テスト
end

group :test do
  gem 'factory_bot_rails' # テストデータ作成ツール
  gem 'rspec-rails' # RSpec 本体
  gem 'simplecov' # カバレッジ計測
  gem 'spring-commands-rspec' # spring を使った RSpec 高速化
end
```

## 設定

```shell
bundle install
bundle exec rails g rspec:install
bundle exec overcommit --install
bundle exec spring binstub rspec
```

### RSpec / FactoryBot 設定

```ruby
# spec/spec_helper.rb

RSpec.configure do |config|
  # 以下の設定を追加
  # for overvommit pre-push config
  ENV['RAILS_ENV'] ||= 'test'
  require File.expand_path('../config/environment', __dir__)
  require 'rspec/rails'
```

```ruby
# spec/rails_helper.rb

# コメントアウト解除
Dir[Rails.root.join('spec', 'support', '**', '*.rb')].each { |f| require f }


RSpec.configure do |config|
  # テスト中に、FactoryBot.create(:user) ではなく、create(:user) と書けるようになる。
  config.include FactoryBot::Syntax::Methods
end
```

### siplecov 設定

```ruby
# spec/spec_helper.rb
require 'simplecov'
SimpleCov.start 'rails'
```

### overcommit 設定

```yml
# .overcommit.yml
gemfile: Gemfile
PreCommit:
  RuboCop:
    description: "Style check with rubocop"
    enabled: true
    on_warn: fail
    command: ["bundle", "exec", "rubocop"]
PrePush:
  RSpec:
    description: "Auto run unit test with RSpec"]
    enabled: true
    command: ['./bin/spec']
# eslintなどのの自動実行設定もできるので、導入しているなら設定するとよい
```

```shell
bundle exec overcommit --sign
```

## テスト作成

```shell
bundle exec rails g rspec:model test
      create  spec/models/test_spec.rb
```

spec の種類は、`system`、`request`、`model`、`routing`、`view`、`helper`、`mailter`、`job`がある。

### ファクトリを追加

```shell
bundle exec rails g factory_bot:model test
```

## テスト実行

```shell
bin/spec <specファイル>
```

`bin/spec`を使うことで、`spring`の高速化が適用される。
Service 層を追加している場合は、`type :service`を設定するとよい。カスタムタイプで統一するすることで、同種のテスト共通の設定を記述しやすくなる。

### RSpec 実行結果表示

結果の表示形式を選択できる。デフォルトは`progress`。
<http://samurai.ataglance.jp/rspec_format/>

### simplecov カバレッジ確認

`/coverage`に結果が出力される。`coverage/index.html`にアクセスするとブラウザから結果を確認できる。
