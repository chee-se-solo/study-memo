# これから先使いそうな GEM

定番とレガシーに分類してキャッチアップ。基本的に github のスターが少ない GEM は純粋にマイナーか、github が流行りだす前から存在したレガシーに片足突っ込んだかつてのスタンダードのようだ。  
全部が全部そうでもなく、現役の GEM もあるだろうけれど、スター１０００の境界がある程度の目安になるかもしれない。

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

- **rubocop**  
  ruby の lint。自分の.rubocop.yml を持っていればあとは警告を粛々と直せばいいだけ。

- **debase**  
  ruby-debug-ide\*\*  
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

- **ed25519**  
  ed25519 とは、新しい暗号化方式。rsa の次に来た今最も使われている鍵暗号。それを Rails の暗号化に導入する Gem。Capistrano で ed25519 鍵を使って接続するときにも必要になるらしい。

- **bycript_pbkdf**  
  ed25519 対応で必要になる Gem の模様。capistrano はこの Gem にも依存しているらしい。

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

- **cocoon**  
  ネストされたフォームを作る Gem。一対多のモデルの入力フォームを作るときに効果を発揮するらしい。初めて見たけどかなりスターがついているしメジャーな Gem のようだ。  
  <https://github.com/nathanvda/cocoon>  
  <https://freecamp.life/rails-cocoon1/>

- **nested_form**
  ネストされたフォームを作る Gem。cocoon と役割がかぶっているような。どっちが使いやすい窯では調べていない。

- **sanitize**  
   HTML をサニタイズする Gem。標準で用意されているような気がする。より細やかな設定が売りなんだろうか。  
  <https://github.com/rgrove/sanitize>  
  <https://qiita.com/kogax/items/7bd4f2421e1939d0412e>

- **rack-cors**  
  rack アプリケーションに CORS を実装する Gem らしい。かなりメジャーな Gem のようだし、押さえておく価値はありそう。  
  <https://github.com/cyu/rack-cors>

## たまに見る Gem

- **overcommit**  
  precommit などの githooks を設定ファイルで簡潔に記述できる Gem。導入すると生の hooks 系ファイルの内容が複雑化するので好き嫌いが分かれる。

- **aws-sdk / aws-sdk-rails / aws-sdk-s3**  
  AWS で運用するのに便利な Gem。インフラを深堀するときに調べる。深追いしない。

- **google-api-client**
  Goole 公式の Gem。Google API を使うためのライブラリ。今後も見ることはありそうだけど、必要になったときに Google 側の設定などと合わせて調べればいいと思う。
  <https://github.com/googleapis/google-api-ruby-client>
  <https://qiita.com/ts-3156/items/1f84d06e50795a9df4c8>

- **payjp**  
  クレジットカード決済を導入する Gem。PayJP の API をたたくためのライブラリ。  
  <https://github.com/payjp/payjp-ruby>  
  <https://qiita.com/takachan_coding/items/f7e70794b9ca03b559dd>

- **wkhtmltopdf-binary**  
  HTML を PDF に変換して出力してくれるライブラリ。シェルコマンドでこういうのがあるらしく、Gem でインストールするときはこれを入れるらしい。  
  <https://github.com/mileszs/wicked_pdf>

- **wicked_pdf**  
  Ruby からシェルコマンドの wkhtml2pdf を使えるようにしてくれる Gem。上の Gem と組み合わせてシステムから PDF を吐く。  
  <https://github.com/mileszs/wicked_pdf>  
  <https://qiita.com/betti/items/8b65146903cce1ff739c>

- **net-ssh**  
  ruby 上から SSH 接続するためのクライアント。  
  <https://github.com/net-ssh/net-ssh>

- **net-scp**
  ruby 上から SCP 接続するためのクライアント。前提 Gem として net-ssh が必要。
  <https://github.com/net-ssh/net-scp>

- **rubyzip**  
  ruby で zip ファイルを作る。  
  <https://github.com/rubyzip/rubyzip>  
  <https://qiita.com/Wa_takumi/items/c9e7a40fcc7ae35a553e>

- **newrelic_rpm**  
  NewRelic の公式 Gem。パフォーマンス計測サービスらしい。  
  <https://newrelic.com/jp>  
  <https://github.com/newrelic/newrelic-ruby-agent>

- **oj**  
  JSON パーサ。高速さが人気のようだ 。  
  <https://github.com/ohler55/oj>  
  <https://thr3a.hatenablog.com/entry/20180517/1526486992>

- **database-cleaner**  
  データベースのデータを消去する Gem。単体テストで重宝する。  
  <https://github.com/DatabaseCleaner/database_cleaner>

- **aws-sdk-ses / aws-sdk-sns**  
  AWS の API を実装する Gem。SES はメール送信サービスのようだ。

- **rest-client**  
  REST リクエストを簡単に作れる Gem。テストツールか、あるいは外部の API をたたくためのツールのようだ。  
  <https://github.com/rest-client/rest-client>

- **slack-notifier**  
  Rails から Slack にメッセージを送信する API。例外発生を検知して通知したりできる。  
  <https://github.com/stevenosloan/slack-notifier>

- **pre-commit**  
  git-hooks の pre-commit でコードの規約チェックをしてくれる Gem。rubocop と overcommit を組み合わせたようなことがシンプルにできるようだ。  
  <https://github.com/pre-commit/pre-commit>

- **faker**  
  氏名やメールアドレスなどのそれっぽいダミーデータを生成する Gem。確かにこういうのがあると便利だ。  
  <https://github.com/faker-ruby/faker>

- **request-store**  
  リクエストごとにリセットされるグローバル変数。ビジネスロジックで使うなら session で足りる気がする。flash スコープ的な使い方が便利なんだろうか。  
  <http://nekorails.hatenablog.com/entry/2018/10/19/202853>  
  <https://github.com/steveklabnik/request_store>

- **paranoia**  
  論理削除を ActiveRecord に仕込む Gem。これ使うくらいならめんどくさくても自分で制御したほうがわかりやすいとかいわれた懐かしい記憶。  
  <https://github.com/rubysherpas/paranoia>

- **omniauth-google-oauth2**  
  この GEM ももうレガシーだろうなと思ったら意外と新しい紹介記事がある。まだ現役だったのか。  
  <https://github.com/zquestz/omniauth-google-oauth2>

- **prawn / prawn-table**  
  PDF を作成する GEM。HTML が組めるなら wicked-PDF でいいと思う。  
  <https://github.com/prawnpdf/prawn>

- **rqcode**  
  QR コードを生成する GEM。使う機会が来たら思い出す程度でよさそう。  
  <https://github.com/whomwah/rqrcode>

- **exception_notitification**  
  例外発生時にメールを送信させるのに便利らしい。まだ使えそうだが、メンテナンスは止まっているように見えるので時期にレガシー化するのかもしれない。  
  <https://github.com/smartinez87/exception_notification>

## ナニコレな Gem

- **ox**  
  XML パーサ。OJ ほどメジャーではないようだ。  
  <https://github.com/ohler55/ox>

- **php-serialiation**  
  PHP の serialize/unserialize メソッドを ruby に実装する Gem。使いたい場面があるのだろうか？

- **json**  
  JSON の parse/serialize を実装する Gem。デフォルトで全く同じ書き方ができたはずだが、高速化した Gem のようだ。  
  <https://github.com/flori/json>

- **intercom**  
  顧客管理サービス Intercom の API。Intercom について調べる必要が出たら別トピックにする。たぶんその機会はないだろうけど。  
  <https://www.intercom.com/>  
  <https://github.com/intercom/intercom-ruby>  
  <https://developers.intercom.com/building-apps/docs>

- **font-awesome-rails**  
  font-awesome を導入する Gem。

- **momentjs-rails**
  momentjs を導入する Gem。

- **datetimepicker-rails**  
  datepicker を導入する Gem。

- **logger-ltsv**  
  LTSV 形式のロガー。Label Tab Separated Value かな。ログをタブ区切りにして、情報にラベルを張るらしい。つまりパースしやすい形式で記述してツールにとっての可読性を上げようというお話のようだ。

- **rails-controller-testing**  
  テスト用の GEM  
  <https://github.com/rails/rails-controller-testing>

- **select2-rails**  
  副背う選択できるセレクトボックスコンポーネント。選択するとタグになるタイプのセレクトボックスを作成できる。  
  <https://remonote.jp/rails-multiselect-select2-jquery>  
  <https://github.com/argerim/select2-rails>

- **net-sftp**  
  SFTP を ruby から使えるようにする GEM。  
  <https://github.com/net-ssh/net-sftp>

- **slack-ruby-client**  
  slack-notifier とは違うのか？
  <https://github.com/slack-ruby/slack-ruby-client>

- **switch_user**  
  ログイン/ログアウトなしで目的のユーザーとしてログインできる開発用 GEM、だろうか。

- **phonelib**  
  電話番号バリデータ。電話番号の国際対応に便利なようだ。  
  <https://github.com/daddyz/phonelib>  
  <https://blog.beaglesoft.net/entry/2019/06/04/094516>  
  <https://qiita.com/Daniel_Nakano/items/75b653fc398d78570e41>

- daemons  
  ruby スクリプトをデーモン化して起動しっぱなしにする GEM。何がうれしいのかよくわからなかった。  
  <https://github.com/thuehlinger/daemons>  
  <https://www.xmisao.com/2013/09/28/bestgems-pickup-daemons.html>

- **delayed_job_acive_record**  
  Active Record で非同期処理を実装してくれるらしい。  
  <https://github.com/collectiveidea/delayed_job_active_record>  
  <https://qiita.com/azusanakano/items/1d2629763f35b5466286>

- **restforce**
  salesforce の GEM。使うことがあったら調べればよさそう。
  <https://github.com/restforce/restforce>

## レガシーになった Gem

- **webpay**  
  その昔の決済サービス。WebPay は PayJP に統合されたのでまさに役目を終えた Gem。

- **fast_jsonapi**  
  名前の通り。具体的な使い方は調べていないが、メンテナンスされていないようだ。

- **google_drive**  
  Rails から Google Drive API をたたくと連携する Gem。メンテナンスされていないようだ。  
  <https://github.com/gimite/google-drive-ruby>

- bootstrap-sass
  最新の bootstrap に対応していない。いまなら bootstrap-rubygem という gem になるらしい。そして Rails6 では webpacker で導入するのが正道になりそうだ。

- **chrome-driver-helper**
  サポートが終了した GEM。Chrome で開発するときに便利だったのだろう。今なら webdriver を使うらしい。

## 最新版から消えたデフォルト Gem

- uglifier  
  JS 軽量化 gem。ES6 に対応していない。

- coffee-rails  
  CoffeeScript を Rails に組み込む gem。Webpacker と入れ替えになった。

- jquery-rails(\*)  
  jquery が全盛の時代のころはデフォルトだったらしい。

- mini_racer
  ruby から JavaScript を実行する gem。これも Webpacker 導入=node 導入でパージされた。
