# ActiveResource 調査

[sandbox project](https://github.com/chee-se/rails7-api-rails4-front)

なんだか使うことになりそうなので調査。

## ActiveResource とは

Rails 3 の標準 Gem。Rails 4 から標準から外れた。
ActiveRecord っぽいインターフェースでモデルを操作して API 通信する。

## 構築

API モードの Rails と NoDB のフロント Rails を用意して ActiveResource で通信させる。

### API Rails 構築

Blocked host エラー対策を設定する（cf. DNS リバインディング攻撃）

app/config/environments.rb

```ruby
  # http://api/ のリクエストを受け付ける
  config.hosts << 'api'
```

scafold で一式を作成する

```shell
  bin/rails g scaffold user last_name:string{20} first_name:string{} nickname:string{20} email:string{50} age:integer
```

### フロント Rails 構築

まず、フロント Rails から ActiveRecord を削除して ActiveResource をインストールする。

```shell
bin/rails new . --skip-activerecord
```

Gemfile

```ruby
# SQLite アダプタをコメントアウト
# gem 'sqlite3'

# ActiveResource を追加
gem 'activeresource', '~> 4.1.0'
```

app/config/application.rb

```ruby
# activerecord 以外をロード
# require 'rails/all'
require 'rails/all'
%w(
  action_controller
  action_view
  action_mailer
  active_job
  rails/test_unit
  sprockets
).each do |framework|
  begin
    require "#{framework}/railtie"
  rescue LoadError
  end
end

# activerecord の設定を削除
# config.active_record.raise_in_transactional_callbacks = true
```

app/config/environments/development.rb

```ruby
 # マイグレーションの設定を削除
 # config.active_record.migration_error = :page_load
```

app/test/test_helper.rb

```ruby
# DB テストの設定読み込みを削除
# fixtures :all
```

#### ActiveResource モデルを作成

scaffold で一式作成

```shell
  bin/rails g scaffold user last_name:string{20} first_name:string{} nickname:string{20} email:string{50} age:integer
```

app/models/user.rb

```ruby
# frozen_string_literal: true

class User < ActiveResource::Base
  self.site = "http://api:3000"
end
```

## 試してみる

フロントにアクセスする（3003 ポートでアクセスできるようにしている）

http://localhost:3000/users

development.log（フロント）

```log
Started GET "/users" for 192.168.80.1 at 2023-01-21 00:32:20 +0900
Cannot render console from 192.168.80.1! Allowed networks: 127.0.0.1, ::1, 127.0.0.0/127.255.255.255
Processing by UsersController#index as HTML
Rendered users/index.html.erb within layouts/application (0.9ms)
Completed 200 OK in 325ms (Views: 265.9ms)
```

development.log（API）

```
Started GET "/users.json" for 192.168.80.2 at 2023-01-21 00:32:20 +0900
Processing by UsersController#index as JSON
  [1m[36mUser Load (0.3ms)[0m  [1m[34mSELECT `users`.* FROM `users`[0m
  ↳ app/controllers/users_controller.rb:8:in `index'
Completed 200 OK in 2ms (Views: 1.7ms | ActiveRecord: 0.3ms | Allocations: 818)
```

json でコンテナ間通信が起きている。
フロントで`@users = User.all`など ActiveRecord っぽく書くと、ActiveResouce が条件から URL を発行し、API に問い合わせる。
そして、API からの JSON レスポンスをパースして`@users`に入れてくれる。
`save`メソッドで POST するなどの対応もしている。

## 懸念点

ActiveResource モデルがカラム情報を一切持っておらず、JSON をパースして得た情報のみでアクセサメソッドを作っている。
レスポンスで一件以上取得できていればよいが、0 件の場合、空の JSON が返ってくるので、カラム情報がなく、属性にアクセスすると NoMethodError が起きる。
つまり、モデルを検索せずに表示する画面（/user/new とか） を開くとエラーが起きて表示できない。
attr_accesor で getter/setter を作ってカラム名を持たせると、JSON をパースして得られた値にアクセスできず`nil`が取得される。
あとは、timestamp 型が文字列になっているので使用する場合は、自前でその手の変換をかけないといけないのもめんどくさそう。

## 残りの確認点

- [ ] ActiveResource のアソシエーションを試す。
- [ ] ActiveResource は where でどのようなリクエストが発行されるか調べる。
