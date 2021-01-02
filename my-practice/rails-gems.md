# これから先使いそうな GEM

定番とレガシーに分類してキャッチアップ

## デフォルト gem

- **rails / mysql2 / puma / sass-rails**  
  語ることなし。調べるとしたら独立したトピックで。

- **tzinfo-data**  
  タイムゾーン用 Gem。タイムゾーン情報を持たない Windows 用。

- **webpacker**  
  Rails 6 のデフォルト。ただの Webpack だけど、Rails の自動ビルドや自動生成に組み込むために Gem 化されている。これはフロントエンドのトピックで webpack を深堀する。webpacker はしない。

- **turbolinks**  
  リンク先を Ajax で取得して body 内をまるごと書き換えることで高速化する。調べない。

- **jbuilder**  
  オブジェクトを JSON 化して返す。それぞれの目的に合わせてより使いやすくした類似 Gem が結構ある。調べない。

- **bycrypt(\*)**  
  rails デフォルト（オプション）暗号化 Gem。モデルに`password_digest`というカラムを持たせて、`has_secure_password`を設定することで`password=`の入力を自動で暗号化する。また、モデルに `authenticate` というメソッドが生えてハッシュ化して比較した結果を返してくれる。

- **image_processing(\*)**  
  rails デフォルト（オプション）の画像加工ライブラリ。中身は ImageMagick。Active Strage で使うらしい。

- **bootsnap**  
  起動高速化 gem。`require`のファイル走査などをキャッシュするらしい。

## development & test group

- **byebug**  
  現代の debugger。コードに仕込むメソッドも`byebug`になったらしい。

## development group

- **listen**  
  ファイルの変更イベントを検知してサーバーを更新する。ファイル変更検知にはイベント検知型とポーリング型があるが、これは前者。

- **web-consle**  
  web ページの下部にデバッグコンソールが埋め込まれる。変数の内容を確認したりできる。すごい。

- **rack-mini-profiler**  
  ページの左上に小さなカードでレスポンスにかかった秒数を表示する。より詳細な数値を見ることもできる。

- **spring**  
  高速化 gem。使うならインストールが必要。`bin/\*`をキャッシュして高速化する。`bundle exec`は対象外らしい。

## test group

- **capybara**  
  ヘッドレスブラウザを使ったテストツール。Rails は API サーバー化が進んでいく気がするので勉強しない。  
  <https://qiita.com/morrr/items/0e24251c049180218db4>

- **selenium-webdriver**  
  ブラウザを自動操作してくれるテストツール？スクレイピングにも使えるらしい。
  <https://qiita.com/y-agatsuma/items/ea2c9845ee0a931d5c9>

- **webdrives**  
  上記の前提 Gem？これもテストに使うらしい。深追いしない。

## 使えるようになっておきたい定番 Gem

- **slim-rails / slim_lint**
  erb に代わるテンプレート。記述量が少なくてすっきりする。

- pry(-rails)
- capistrano
- capistrano-bundler
- capistrano-rails
- capistrano3-puma
- capistrano-rbenv
- html2slim
- rubocop
- debase
- ruby-debug-ide
- device
- devise-encryptable
- cancancan

## 覚えておくとよさそうな Gem

## ナニソレな Gem

## 以下、分類しながら整理

- overcommit
- ed25519
- bycript_pbkdf
- record_with_operator
- php-serialiation
- aws-sdk
- aws-sdk-rails
- bootstrap-sass
- breadcrumbs_on_rails
- json
- kaminari
- composite_primary_keys
- google-api-client
- webpay
- payjp
- config
- wicked_pdf
- wkhtmltopdf-binary
- net-ssh
- net-scp
- sanitaize
- activerecord-import
- rubyzip
- whenever
- cocoon
- intercom
- rack-cors
- newrelic_rpm
- restforce
- font-awesome-rails
- momentjs-rails
- datetimepicker-rails
- simple_form
- nested_form
- composite_primary_keys
- oj
- ox
- activerecord-import
- factory-bot-rails
- database-cleaner
- simplecov
- logger-ltsv
- fast_jsonapi
- aws-sdk-sns
- sws-sdk-ses
- rest-client
- slack-notifier
- google_drive
- rails-controller-testing
- pre-commit
- faker
- database_cleaner
- request-store
- paranoia
- select2-rails
- omniauht-google-oauth2
- prawn
- prawn-table
- net-sftp
- slack-ruby-client
- rqrcode
- switch_user
- rubyzip
- exception_notification
- phonelib
- aws-sdk-s3
- daemons
- validate_url
- chrome-driver-helper
- delayed_job_acive_record
- exception_notification

## 最新版から消えたデフォルト Gem

- uglifier  
  JS 軽量化 gem。ES6 に対応していない。
- coffee-rails  
  CoffeeScript を Rails に組み込む gem。Webpacker と入れ替えになった。
- jquery-rails(\*)  
  jquery が全盛の時代のころはデフォルトだったらしい。
- mini_racer
  ruby から JavaScript を実行する gem。これも Webpacker 導入=node 導入でパージされた。
