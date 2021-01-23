# RSpec メモ

## spec の種類

- SystemSpec / Feature Spec
  ブラウザを通してテストする

- Request Spec
- ~~Controller Spec~~
  非推奨
- Model Spec
- Routing Spec
- View Spec
- Helper Spec
- Mailer Spec
- Job Spec

spec の基本

```rb
describe テスト対象, type: specの種類 do
  context テスト条件 do
    before do
      セットアップ
    end

    it テスト内容 do
      機能の実行と、期待する結果の検証
    end
  end
end
```

## FactoryBot

ファクトリを定義して柔軟かつ簡潔にモデルを作成、登録できる。

ファクトリの基本

```ruby
FactoryBot.define do
  factory :ファクトリ名, class: モデルクラス do
    属性 { 値 }
    ...
    association 関連（ファクトリ名）
  end
```

ファクトリ名とモデル名が一致する場合、`class`指定は省略可能。  
`create`、`build`メソッドで登録、あるいは登録なしでインスタンスの作成ができる。  
作成時にファクトリで定義した値を上書きすることもできる。  
ファクトリ内にネストしてファクトリを記述することで継承ファクトリを作ることができる。  
また、ネストせずに`parent`していで親ファクトリを指定して継承ファクトリを作ることもできる。

## let

遅延評価変数。テストごとに初期化され、実行時に評価される。  
複数回呼び出される場合は、初回のみ評価され、二回目以降は初回の値を返す。  
どこで宣言しようとブロック内外のすべてで使えるが、二か所以上で宣言されていた場合、ブロックスコープ内で宣言したほうのみ評価される。（let は上書きされる）
