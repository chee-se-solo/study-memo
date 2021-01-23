# 小さいトピックの詰め合わせ

- **パーシャルテンプレートではインスタンス変数を使わない**

  - ✔️ **依存をなくして再利用性を高めるため**
    ❓ **依存は悪か？**
    別にパーツを表示するために必要な変数に依存するのだから何も問題ない気もするが、繰り返し表示したくなったり大きめのコンポーネントとして扱いたくなることもあるだろうか。必ず守るべき鉄則というわけではなさそうだが、大した手間でもないし将来の使い方なんてわかったものじゃないから守っておいた方が無難か。

## CSP(Content Security Policy)

参考 URL：

- <https://railsguides.jp/security.html#content-security-policy%EF%BC%88csp%EF%BC%89>
- <https://developer.mozilla.org/ja/docs/Web/HTTP/CSP>

セキュリティ系のヘッダを追加する設定。
HTTPS を強制する、JS や CSS などの取得ドメインを限定する、などの制限をかけられる。ポリシーに違反した場合にサーバーに JSON でレポートを送ったりもできる。

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
