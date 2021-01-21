# Active Storage 使い方

使ってみたので備忘として残す。クラウドサービスへの保存はしない予定。使う場合は config/storage.yml にサービス情報を記述して、credentials.yml.enc に接続情報を持たせておく。master.key は環境変数で本番に注入する。

## インストール

```shell
bin/rails active_storage:install
bin/rails db:migrate
```

`active_storage_blobs`と`active_storage_attachments`と`active_storage_variant_records`テーブルが作られる。

- active_storage_blobs  
  アップロードされたファイルに関するメタ情報。ファイル名、コンテントタイプ、メタ情報、ファイルサイズ、チェックサムなどが保存されている。

- active_storage_attachments  
  blobs とモデルを関連付ける中間テーブル。ポリモーフィック関連で各テーブルへの FK を持っている。

- active_storage_variant_records  
  保存場所を外部のストレージサービスにしたときに、圧縮やリサイズなどの変更をかけたバリアントが存在するかどうかを保存するテーブル。問い合わせで発生する通信のオーバーヘッドを抑制するためのキャッシュ。

## モデルに画像をもたせる

モデルに`has_one_attached`か`has_many_attached`で、画像用フィールド名を定義する。テーブルに専用のカラムを作る必要はない。  
モデルが画像を持つかどうかは画像フィールドに生えている`attached?`メソッドで確認できる。  
Strong Parameter でフィールドを許可しておかないとリクエストから取り出すことができないので注意。

## 画像を画面上で扱う

画面から送信させるときはフォームヘルパの`f.file_field`でフォームを用意すればよい。
表示するときは、`image_tag`ヘルパに画像フィールドを渡せば表示してくれる。  
あるいは、`url_for`ヘルパに画像フィールドを渡せば画像 URL を取得できる。

## その他、細かい設定

### アップロードしたファイルのパスを変更する

application.rb で以下を追加

```ruby
  config.active_storage.routes_prefix = '/xxxxxx'
```

これでファイルへの URL が`http://[アプリケーションパス]/xxxxxx/blobs/[自動生成パス]`になる。
