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
  erb に代わるテンプレート。記述量が少なくてすっきりする。使い方自体は簡単そうなので、調査というより実践で理解する。

- **html2slim**  
  erb を slim に変換する gem。slim を導入するときに便利。使い終わったら決してよさそうだ。

- **pry / pry-rails / pry-byebug / pry-stack_explorer)**  
  対話型コンソール。`irb`をより高機能にしたもの。ドキュメントやソースコードの確認、フィールドやメソッドの一覧と詳細、シェルモードでシェルコマンドを使うなどできるらしい。  
  <https://qiita.com/k0kubun/items/b118e9ccaef8707c4d9f>

- **capistrano / capistrano-bundler / capistrano-rails / capistrano3-puma capistrano-rbenv**  
  デプロイツール。それぞれの環境に最適化したプラグインが用意されている。rbenv 下の ruby に PATH を通すとかいろいろな設定ができるんだろうと思う。
  <https://qiita.com/tkykmw/items/a34441aae142e0e41b65>  
  上記が capistrano 導入の手順、それぞれのプラグインの役割の両方で参考になりそうだ。

- rubocop
  ruby の lint。自分の.rubocop.yml を持っていればあとは警告を粛々と直せばいいだけ。

- **debase  
  ruby-debug-ide**  
  デバッグ用 gem。debase は何をしているのかよくわからなかったが、デバッグの前提になる gem らしい。ruby-debu-ide は文字通り IDE からデバッグできるようにする gem。

- **devise / devise-encryptable / devise_token_auth**  
  認証の定番。簡単に導入できる反面、中で何をやっているかわかりにくいと評判。とりあえず導入の仕方を理解すれば、プロジェクトで何をしているかもりかいできるだろう。具体的に何をしているかは後回しでもいい。
  <https://qiita.com/hakatatech/items/ea0d9236d89fa24a7a1c>  
  <https://qiita.com/Hal_mai/items/350c400e8763ce0487a3>  
  <https://kitsune.blog/rails-devise>

- **cancancan**  
  権限系の定番。とても単純。`can`で権限を与え、`can?`で権限をチェックする。Abillity クラスで付与する権限を記述して、権限が必要な各所でチェックする使い方のようだ。  
  <https://qiita.com/umanoda/items/679419ce30d1996628ed>

- **kaminari**  
  ページングの定番。page と per を指定するだけで表示する内容を絞り、ページャを作ってくれる。  
  <https://qiita.com/residenti/items/1ae1e5ceb59c0729c0b9>

- **will_paginate**  
  ページングの定番 2。kaminari と大差ないように見える。  
  <https://qiita.com/Nukioman/items/5e35ded48dfcab389c75>

- **config**  
  環境ごとに定数を分けるときに使う。それぞれの環境での定数を yml で定義できる。  
  <http://vdeep.net/rubyonrails-config-gem>

- **whenever**  
  cron 設定を ruby で書ける。普通に cron を書けばいいような気もするが、プロジェクトごとの cron 設定をプロジェクトのリポジトリ内で管理できると言われれば確かにありがたいのかもしれない。
  <http://vdeep.net/rubyonrails-config-gem>

- **simple_form**  
  Rails のフォームをシンプルにしたもの、ではなく逆にオプションを追加したような gem。記述はほぼ変わらないが細かなオプションが追加されていて、フォームのラッパーなどをオプションで指定することでフォームヘルパーが生成してくれるようになる。テンプレートの記述がシンプルになる gem。

## 覚えておくとよさそうな Gem

- **record_with_operator**  
  モデルにトレース情報を追加してくれる gem。シンプルなので公式の README が一番わかりやすい。  
  <https://github.com/nay/record_with_operator>

- **breadcrumbs_on_rails**  
  パンくずを作る。シンプル。
  <https://qiita.com/stbtkhr/items/8170a78d085c6413f9c1>

- **activerecord-import**  
  bulk insert を ActiveRecord に導入する。Rails6 で bulk insert が追加されたのでこの gem もいずれはレガシーになるのかもしれない。
  <https://github.com/zdennis/activerecord-import>

- **validates_url**  
  URL のバリデーションをする Gem。カスタムバリデーションで検査したくなる内容が用意されているようだ。
  <https://github.com/perfectline/validates_url>

- **factory-bot-rails**  
  かつての FactryGirl。テストデータを定義して量産できる。
  <https://qiita.com/morrr/items/f1d3ac46b029ccddd017>

- **composite_primary_keys**  
  モデルに複合キーを導入する。古いシステムのための gem では。  
  <https://qiita.com/hmuronaka/items/4d7f4bb4cfe1be509bc7>

- **simplecov**
  カバレッジを表示する gem。
  <https://qiita.com/komatsubara/items/02962feb28a9eb7e9123>

> しかし Rails について検索した時の Qiita 率はすごいな

## ナニソレな Gem

## 以下、分類しながら整理

- overcommit
- ed25519
- bycript_pbkdf
- php-serialiation
- aws-sdk
- aws-sdk-rails
- json
- google-api-client
- webpay
- payjp
- wicked_pdf
- wkhtmltopdf-binary
- net-ssh
- net-scp
- sanitaize
- rubyzip
- cocoon
- intercom
- rack-cors
- newrelic_rpm
- restforce
- font-awesome-rails
- momentjs-rails
- datetimepicker-rails
- nested_form
- composite_primary_keys
- oj
- ox
- database-cleaner
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

## レガシーな gem

- bootstrap-sass
  最新の bootstrap に対応していない。いまなら bootstrap-rubygem という gem になるらしい。そして Rails6 では webpacker で導入するのが正道になりそうだ。
