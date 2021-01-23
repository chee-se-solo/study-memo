# 小さいトピックの詰め合わせ

## パーシャルテンプレートではインスタンス変数を使わない

- ✔️ **依存をなくして再利用性を高める k**
  ❓ **依存は悪か？**
  別にパーツを表示するために必要な変数に依存するのだから何も問題ない気もするが、繰り返し表示したくなったり大きめのコンポーネントとして扱いたくなることもあるだろうか。必ず守るべき鉄則というわけではなさそうだが、大した手間でもないし将来の使い方なんてわかったものじゃないから守っておいた方が無難か。

## コーディング規約

- Ruby Association <https://www.ruby.or.jp/ja/tech/development/ruby/050_coding_rule.html>
- rubocop style guide <https://github.com/rubocop-hq/ruby-style-guide>

## バージョンアップ

こまめにバージョンアップすることで、バージョンアップにかかるコストを平坦化して負担を軽くする。

- `bundle update`し、アップデート内容を changelog から確認する
- バージョンアップに問題のある gem のバージョンを戻す
- コードレビューの後にコード変更をリポジトリに取り込む

バージョンを差し戻した gem はバージョンを固定し、理由と固定解除の条件をコメントで記載しておくこと。  
週１で実施する、半自動化するなどの取り組みもできる。

## バージョンアップ（メジャーバージョン）

バージョンアップへの取り組みが長くかかることを前提とする。
以下について入念に検討しておく

- テスト戦略（自動テストは十分に実施可能か）
- 動作確認戦略（ステージング環境を利用する、専用環境を利用する）
- リリース戦略（一度にリリースする、並行稼働環境を作り段階的にリリースする）
- リリース後の状況の監視（レスポンスタイム、エラー発生状況、バージョンアップ前にはなかったエラーが発生していないか）
- ロールバック準備（万一の場合の備え。非互換の問題はロールバック時に問題が起きないように対策しておく）

## CSP(Content Security Policy)

参考 URL：

- <https://railsguides.jp/security.html#content-security-policy%EF%BC%88csp%EF%BC%89>
- <https://developer.mozilla.org/ja/docs/Web/HTTP/CSP>

セキュリティ系のヘッダを追加する設定。
HTTPS を強制する、JS や CSS などの取得ドメインを限定する、などの制限をかけられる。ポリシーに違反した場合にサーバーに JSON でレポートを送ったりもできる。

## 秘密情報の取り扱い

- config/master.key  
  credentials や暗号化 Cookie や署名付き Cookie の整合性確認に使用する。

  - 環境変数`RAILS_MASTER_KEY`に設定してもよい。

- credentials.yml.enc  
  暗号化された yml ファイル。コマンドを通して編集、閲覧する。  
  編集する際のエディタは環境変数`EDITOR`が使われる。

  ```shell
  # 閲覧
  bin/rails credentials:show

  # 編集
  bin/rails credentials:edit
  ```

  ```ruby
  # 読み出し
  Rails.application.credentials.some_key
  ```

- Encrypted  
  credentials を一般化した機能。任意のファイル名の暗号化 yml を任意のキーで作成できる。キーを指定しない場合は`master.key`が流用される。

  ```shell
  # 閲覧
  bin/rails encrypted:show config/staging-credentials.yml.enc --key config/staging.key
  # 編集
  bin/rails encrypted:edit config/staging-credentials.yml.enc --key config/staging.key
  ```

  ```ruby
  # key の指定はいらないのか？Encrypted に関する記述が公式ガイドに見当たらない
  Rails.application.encrypted("config/staging-credentials.yml.enc").some_key
  ```

## I18n（日本語化）

参考 URL：

- <https://guides.rubyonrails.org/i18n.html>
- <https://qiita.com/lhside/items/52623ca8d09858fc7d6e>
- <https://qiita.com/tsunemiso/items/bedc7593c7ccd05c395b>

言語ファイルを`config/locale`以下に配置する。

```shell
wget https://raw.githubusercontent.com/svenfuchs/rails-i18n/master/rails/locale/ja.yml -P config/locales/
```

デフォルトロケールを設定する。

```ruby
# config/application.rb
Rails.application.config.i18n.default_locale = :ja
```

多言語化する場合は下記を加える。

```ruby
# config/application.rb
config.i18n.available_locales = %i(ja en)
# 対応外の言語をエラーとする場合
config.i18n.enforce_available_locales = true
```

ロケールの切り替え

```ruby
I18n.locale = :ja
```

モデル名やエラーメッセージなどの自動生成以外の単語、文言を多言語化する場合

```ruby
# 文言をlocaleに合わせて変換
t('dictionary.button.search')
# 日時をlocaleに合わせて変換
l(model_name_A.created_at, format: :default)
```

## デフォルトのタイムゾーンを変更する

```ruby
# app/config/application.rb

    config.time_zone = 'Asia/Tokyo'

```

参考 URL：
<https://qiita.com/aosho235/items/a31b895ce46ee5d3b444>

## 非同期処理ツールの導入

`sidekiq`を使う

```shell
sudo apt install redis-server
redis-server
```

```ruby
# Gemfile

gem 'sidekiq'
```

```shell
bundle exec sidekiq
```

```ruby
# config/environments/development.rb

  config.active_job.queue_adapter = :sidekiq
```

```shell
bin/rails g job sample
```

```ruby
# app/jobs/sample_job.rb

class SampleJob < ApplicationJob
  queue_as :default

  def perform(*args)
    Sidekiq::Logging.logger.info "サンプルジョブを実行しました"
  end
end
```

```ruby
# app/controllers/tasks_controller.rb

  def create
    ...
    # タスクのキューイングのみ行う。待ち行列に追加し、実行可能になったタイミングで実行する
    SampleJob.perform_later

    # 明日正午に実行する
    SampleJob.set(wait_until: Date.tomorrow.noon).perform_later

    # １週間後に実行する
    SampleJob.set(wait: 1.week).perform_later
    ...
  end
```
