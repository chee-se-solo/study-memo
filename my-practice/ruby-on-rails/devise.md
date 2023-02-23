# Devise 覚書

大体のプロジェクトで使用されているが、自分で構築したことがなかったのでやってみる。
取り回しは何となく分かるので、全体をドキュメントで改めて確認しつつ、ロールや権限の追加周り、2 段階認証周りを抑えていきたい。

追記
おおよそこれらの記事の後追いになった。

https://qiita.com/cigalecigales/items/16ce0a9a7e79b9c3974e

https://nllllll.com/ruby-on-rails/rails-devise/

カスタマイズする上では公式 wiki とソースリーディングが一番頼りになった  
https://github.com/heartcombo/devise/wiki

## devise インストール

```shell
bundle install
bin/rails generate devise:install
```

## devise 設定

```ruby
config/environments/develoment.rb

### ルート URL を定義

config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

```

### ルートアクションを定義

デバイス用のルーティングを別ファイルに定義。  
`resources` より先に読み込まないと、`/users/***` が `/users/:id` に一致して `devise`のルーティングが効かなくなるので注意。  
`/users/sign_in` を`root`にしたかったが、`devise_scope` 内で定義しても `devise_mapping` でエラーになったので一旦諦め。

[devise のルーティングについて](http://www.code-magagine.com/?p=13096)

```ruby
config/routes.tb
  draw :devise
  resources :users
  root to: "home#index"
```

```ruby
Rails.application.routes.draw do
  devise_for :users, module: 'devise/users'
end
```

### ビューにメッセージを追加

app/views/layouts/application.html.erb.

```erb

<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

### devise ビューを生成

後に admin を作ることを想定して、devise/users 名前空間を作る

```shell
bin/rails g devise:views devise/users
      invoke  Devise::Generators::SharedViewsGenerator
      create    app/views/devise/users/shared
      create    app/views/devise/users/shared/_error_messages.html.erb
      create    app/views/devise/users/shared/_links.html.erb
      invoke  form_for
      create    app/views/devise/users/confirmations
      create    app/views/devise/users/confirmations/new.html.erb
      create    app/views/devise/users/passwords
      create    app/views/devise/users/passwords/edit.html.erb
      create    app/views/devise/users/passwords/new.html.erb
      create    app/views/devise/users/registrations
      create    app/views/devise/users/registrations/edit.html.erb
      create    app/views/devise/users/registrations/new.html.erb
      create    app/views/devise/users/sessions
      create    app/views/devise/users/sessions/new.html.erb
      create    app/views/devise/users/unlocks
      create    app/views/devise/users/unlocks/new.html.erb
      invoke  erb
      create    app/views/devise/users/mailer
      create    app/views/devise/users/mailer/confirmation_instructions.html.erb
      create    app/views/devise/users/mailer/email_changed.html.erb
      create    app/views/devise/users/mailer/password_change.html.erb
      create    app/views/devise/users/mailer/reset_password_instructions.html.erb
      create    app/views/devise/users/mailer/unlock_instructions.html.erb
```

### devise model 作成

```shell
bin/rails g devise user
bin/rails db:migrate
```

```ruby
# マイグレーションファイル ***_devise_create_user.rb
  def change
    create_table :users do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      # t.integer  :sign_in_count, default: 0, null: false
      # t.datetime :current_sign_in_at
      # t.datetime :last_sign_in_at
      # t.string   :current_sign_in_ip
      # t.string   :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
```

```ruby
# 自動生成されたモデル
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable, :trackable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :validatable
end
```

### devise controller 設定

bin/rails g devise:controller devise/users

```shell
bin/rails g devise:controllers devise/users
      create  app/controllers/devise/users/confirmations_controller.rb
      create  app/controllers/devise/users/passwords_controller.rb
      create  app/controllers/devise/users/registrations_controller.rb
      create  app/controllers/devise/users/sessions_controller.rb
      create  app/controllers/devise/users/unlocks_controller.rb
      create  app/controllers/devise/users/omniauth_callbacks_controller.rb
===============================================================================

Some setup you must do manually if you haven't yet:

  Ensure you have overridden routes for generated controllers in your routes.rb.
  For example:

    Rails.application.routes.draw do
      devise_for :users, controllers: {
        sessions: 'users/sessions'
      }
    end

===============================================================================
```

## SNS 連携認証 設定

wip

## 管理者権限追加

wip

### 管理者権限チェック ＆ ユーザーログインチェック

# 確認すること

- [ ] 権限管理
- [ ] ロール管理
- [ ] 認証方法の種類、設定（二段階認証）
- [ ] コントローラー内、ビュー内での権限による切り替え
