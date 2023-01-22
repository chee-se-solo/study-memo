# ActiveResource 調査

[sandbox project](https://github.com/chee-se/rails7-api-rails4-front)

なんだか使うことになりそうなので調査。

## ActiveResource とは

Rails 3 の標準 Gem。Rails 4 から標準から外れた。
ActiveRecord っぽいインターフェースでモデルを操作して API 通信する。

## 参考

公式 Doc（Rails3.2）

- https://api.rubyonrails.org/v3.2.1/classes/ActiveResource/Base.html

個人ブログ

- http://webos-goodies.jp/archives/how_to_use_activeresource_1.html
- http://webos-goodies.jp/archives/how_to_use_activeresource_2.html

いずれにしても古いドキュメントしか見当たらない。特にブログはアップデートされてなさそう。何ができるのかだけ参考にする。

## 構築

API モードの Rails と NoDB のフロント Rails を用意して ActiveResource で通信させる。

### API Rails 構築

API モードの Rails をインストールする

```shell
bin/rails new . --api -d mysql
```

Blocked host エラー対策を設定する（cf. DNS リバインディング攻撃）

app/config/environments.rb

```ruby
  # http://api/ のリクエストを受け付ける
  config.hosts << 'api'
```

scafold で一式を作成する

```shell
  bin/rails g scaffold user last_name:string{20} first_name:string{20} nickname:string{20} email:string{50} age:integer
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
  bin/rails g scaffold user last_name:string{20} first_name:string{20} nickname:string{20} email:string{50} age:integer
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

http://localhost:3003/users

development.log（フロント）

```log
Started GET "/users" for 192.168.80.1 at 2023-01-21 00:32:20 +0900
Cannot render console from 192.168.80.1! Allowed networks: 127.0.0.1, ::1, 127.0.0.0/127.255.255.255
Processing by UsersController#index as HTML
Rendered users/index.html.erb within layouts/application (0.9ms)
Completed 200 OK in 325ms (Views: 265.9ms)
```

development.log（API）

```log
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

## アソシエーション

```shell
bin/rails g scaffold post user:references title:string{50} text:string posted_at:datetime modified_at:datetime
bin/rails db:migrate
```

app から post をリクエストしてみたら、紐づく user も一緒に取得できた。が、lazy load のように裏でリクエストが２つ投げられている。

```log
api_1        | Started GET "/posts.json" for 172.20.0.2 at 2023-01-22 04:55:46 +0900
api_1        | Processing by PostsController#index as JSON
api_1        |   Post Load (0.3ms)  SELECT `posts`.* FROM `posts`
api_1        |   ↳ app/controllers/posts_controller.rb:9:in `index'
api_1        | Completed 200 OK in 30ms (Views: 7.7ms | ActiveRecord: 3.4ms | Allocations: 3875)
api_1        |
api_1        |
api_1        | Started GET "/users/1.json" for 172.20.0.2 at 2023-01-22 04:55:47 +0900
api_1        | Processing by UsersController#show as JSON
api_1        |   Parameters: {"id"=>"1"}
api_1        |   User Load (0.2ms)  SELECT `users`.* FROM `users` WHERE `users`.`id` = 1 LIMIT 1
api_1        |   ↳ app/controllers/users_controller.rb:44:in `set_user'
api_1        | Completed 200 OK in 18ms (Views: 0.3ms | ActiveRecord: 2.1ms | Allocations: 3349)
```

暗黙で N+1 問題を起こしたら N+1 リクエストが起きそう。怖い。
API 側で対策する必要がある。

```ruby
  # GET /posts
  def index
    @posts = Post.eager_load(:user)
    render json: @posts, include: :user
  end
```

`render`にオプションを渡してレスポンスでリレーションをしっかりと含めてやること。

```log
api_1        | Started GET "/posts.json" for 172.20.0.2 at 2023-01-22 04:53:09 +0900
api_1        | Processing by PostsController#index as JSON
api_1        |   SQL (0.5ms)  SELECT `posts`.`id` AS t0_r0, `posts`.`user_id` AS t0_r1, `posts`.`title` AS t0_r2, `posts`.`text` AS t0_r3, `posts`.`posted_at` AS t0_r4, `posts`.`modified_at` AS t0_r5, `posts`.`created_at` AS t0_r6, `posts`.`updated_at` AS t0_r7, `users`.`id` AS t1_r0, `users`.`last_name` AS t1_r1, `users`.`first_name` AS t1_r2, `users`.`nickname` AS
t1_r3, `users`.`email` AS t1_r4, `users`.`age` AS t1_r5, `users`.`created_at` AS t1_r6, `users`.`updated_at` AS t1_r7 FROM
`posts` LEFT OUTER JOIN `users` ON `users`.`id` = `posts`.`user_id` WHERE `posts`.`id` = 2
api_1        |   ↳ app/controllers/posts_controller.rb:6:in `index'
api_1        | Completed 200 OK in 4ms (Views: 0.4ms | ActiveRecord: 0.5ms | Allocations: 1190)
```

公式に記述があった。一対多リソースの場合には、以下のように、as_json をオーバーライドすれば、２つ目のリクエストは抑制されるらしい。
もちろん API 側で JOIN した結果を返す必要がある。

```ruby
class Post < ActiveRecord::Base
  has_many :comments

  def as_json(options)
    super.merge(:include=>[:comments])
  end
end
```

逆に、必要のない多対一アソシエーションは切り落としてやることでアソシエーションループを回避する。
切り落とさない場合、`user_id` から自動的に `user` を取得しにリクエストする

```ruby
  # GET /posts
  def index
    @posts = Post.eager_load(:user)
    render json: @posts, except: :user
  end
```

## 条件分の取り扱い

ActiveResource は`where`が用意されているのでそれで絞り込みをかけることができる。（join 等の連結系のインターフェースはないので、テーブル単体に検索をかけるのみ）

```ruby
User.where(last_name: ['satoh', 'tanaka'], first_name: ['ichiro', 'jiro'])
```

```log
api_1        | Started GET "/users.json?first_name%5B%5D=ccc&first_name%5B%5D=ddd&last_name%5B%5D=aaa&last_name%5B%5D=bbb" for 172.20.0.2 at 2023-01-22 18:11:14 +0900
api_1        | Processing by UsersController#index as JSON
api_1        |   Parameters: {"first_name"=>["ccc", "ddd"], "last_name"=>["aaa", "bbb"]}
api_1        |   User Load (0.3ms)  SELECT `users`.* FROM `users`
api_1        |   ↳ app/controllers/users_controller.rb:7:in `index'
api_1        | Completed 200 OK in 2ms (Views: 1.9ms | ActiveRecord: 0.3ms | Allocations: 817)
```

ただし、条件はハッシュ形式で渡せるもののみに限られる。

```ruby
User.where('age >=', 20)
#=> ArgumentError: wrong number of arguments (2 for 0..1)
```

## スキーマ定義

JSON から属性を生成しているので、通信を行わずに開く画面は、存在しない属性へのアクセス扱いとなり、NoMethodError が発生して表示できない。（`/posts/new`とか）  
そのため、必要であれば自前でスキーマを定義することで問題を回避することができる。

```ruby
class Post < ActiveResource::Base
  self.site = "http://api:3000"

  belongs_to :user

  schema do
    attribute 'user_id', :integer
    attribute "title", :string
    attribute "text", :string
    attribute "posted_at", :string
    attribute "modified_at", :string
  end
end
```

ただし、型変換が貧弱。`fload`、`integer`、`string`しか使えず、他の方は`string`で保持される。  
`datetime`などは手動で変換をかける必要がある。

## カスタムアクション

複雑な URL へのアクセスもサポートしている。以下、リファレンスの引用。  
``

```ruby
# POST to the custom 'register' REST method, i.e. POST /people/new/register.json.
Person.new(:name => 'Ryan').post(:register)

# PUT an update by invoking the 'promote' REST method, i.e. PUT /people/1/promote.json?position=Manager.
Person.find(1).put(:promote, :position => 'Manager')

# GET all the positions available, i.e. GET /people/positions.json.
Person.get(:positions)

# DELETE to 'fire' a person, i.e. DELETE /people/1/fire.json.
Person.find(1).delete(:fire)
```

## テスト

ActiveRecord に API 通信部分をモックしてテストできる仕組みが備わっている。以下、個人ブログのエントリーより引用。

```ruby
require 'test_helper'
require 'active_resource/http_mock'

class BookmarkTest < ActiveSupport::TestCase
  def setup
    @record = {
      :id      => 1,
      :title   => 'WebOS Goodies',
      :url     => 'http://webos-goodies.jp/',
      :comment => 'Welcome!'
    }.to_xml(:root => 'bookmarks')
    @header = Bookmark.connection.__send__(:build_request_headers, {}, :get)
    ActiveResource::HttpMock.respond_to do |mock|
      mock.get '/bookmarks/1.xml', @header, @record
    end
  end

  test "get bookmark" do
    bookmark = Bookmark.find(1)
    assert_equal 'WebOS Goodies',            bookmark.title
    assert_equal 'http://webos-goodies.jp/', bookmark.url
    assert_equal 'Welcome!',                 bookmark.comment
    expected_request = ActiveResource::Request.new(:get, '/bookmarks/1.xml', nil, @header)
    assert ActiveResource::HttpMock.requests.include?(expected_request)
  end
end
```
