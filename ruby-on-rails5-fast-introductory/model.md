# モデルについてのトピックまとめ

## バリデーション

参考 URL：
<https://railsguides.jp/active_record_validations.html>

Rails はモデルでバリデーションをする。Rails ではリクエストをモデルに渡してインスタンス作成とバリデーションを同時に行う作りなので、バリデーションをモデルに記述することになる。

> 永続化層とビジネスロジックのつなぎ役がモデルというイメージなのだが、モデルが永続化層とビジネスロジック層に加えて、プレゼンテーション層のからきたパラメータの処理まで担当したらそれは fat にもなる気がする。とはいえモデルからこぼれたロジックはコントローラーに記述されるから、モデルを活用しないとコントローラーも fat になりうる。  
> 構造を簡略化して素早く作れる反面、大規模開発に向かないという性質も納得できる気がする。  
> ロジックを書く場所がモデルしかないので自然と肥大化していくということだろうか。

```ruby
# app/models/task.rb
class Task < ApplicatoinRecord
  validates: :name, presence: true
end
```

バリデータは、`presence`/`numericality`/`inclusion`/`length`/`format`（正規表現）
他に、DB を参照して一意性を検証する`uniquness`、確認入力欄の一致を検証する`confirmation`がある。  
`validates_associated`というのもあるらしくとても便利そう。オブジェクトの両側で定義すると無限ループになるらしいが。

> バリデーションの段階で DB を覗けるのは前後層が一体化したモデルならでは、だろうか？

### オリジナルのバリデーションを作る

メソッドでバリデーションを実装する。

```ruby
validate :validate_name_not_including_comma

private

def validate_name_not_including_comma
  errors.add(:name, 'にカンマを含めることはできません') if name&.include(',')
end
```

バリデータを実装する(Validator)

```ruby
# app/validators/goddness_validator.rb
class GoodnessValidator < ActiveModel::Validator
  def validate(record)
    if options[:fields].any? { |field| record.send(field) == "Evil" }
      record.errors.add :base, "This person is evil"
    end
  end
end

# app/models/person.rb
class Person < ApplicationRecord
  validates_with GoodnessValidator, fields: [:first_name, :last_name]
end
```

バリデータを実装する(EachValidator)

```ruby
class EmailValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    unless value =~ /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\z/i
      record.errors.add attribute, (options[:message] || "is not an email")
    end
  end
end

class Person < ApplicationRecord
  validates :email, presence: true, email: true
end
```

## コールバック

- **new**
  - after_initialize
- **validate**
  - before_validation
  - after_validation
- **save** / **create** / **update**
  - before_save
  - before_create
  - before_update
  - after_save
  - after_create
  - after_update
- **destroy**
  - before_destroy
  - after_destroy
- **around**
  - around_save
  - around_create
  - around_update
  - around_destroy
- **find**
  - after_find

## パスワードを持つモデル

パスワードのハッシュ化などを自動でやってくれる。`bcrypt` を インストールしておく。

```shell
bin/rails g model user name:string email:string password_digest:string
```

```ruby
class User < ApplicationRecord
  has_secure_password
end
```

モデルに`password`と`password_confirmation`が生えて、一致バリデーションがかかるようになる。また、保存するときに自動的にハッシュ化されて DB に保存される。  
検証する場合は`authenticate`メソッドを使う。

```ruby
user = User.find_by(email: session_params[:email])
if user&.authenticate(params[:password])
  ...
end
```

## モデルの共通化

### 1. モジュールで Mix-in

```ruby
module Commentable
  extend ActiveSupport::Concern

  included do
    has_many :comments, as: :commentable
  end

    # コメントの操作のメソッド群
    ...
end
```

`included`ブロックは`include`したモデル内で実行される。

### STI（単一テーブル継承）

共通機能を基底クラスに持たせる。  
一つのテーブルに複数のモデルのデータが登録される。

```ruby
class Lesson::Base < ApplicationRecord
  self.table_name = 'lessons'
  ...
end

class Lesson::Online < Lesson::Base
  ...
end

class Lesson::School < Lesson::Base
  ...
end
```

### 抽象クラスを作成する

ApplicationRecord とモデルの間に抽象クラスを挟む

```ruby
class UserContentModel < ApllicationRecord
  self.abstract_class = true
  ...
end
```
