# アセットについてのトピックまとめ

## アセットパイプライン

### アセットパイプラインの役割

- トランスパイル（cofee スクリプト、SCSS）
- １ファイルへバンドル
- minify
- ハッシュ付与

### アセットパイプラインを利用する

以下のヘルパで読み込む

```slim
= stylesheet_include_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'
= javascript_include_tag 'application', 'data-turbolinks-track': 'reload'
```

マニフェストファイルがエントリーポイントになる。

- app/assets/application.css
- app/assets/application.js

これらのファイルからインクルードされた JS、CSS がコンパイル・バンドルされて HTML から読み込まれる。

### マニフェストファイルの記述

```js
// app/assets/javascripts/application.js

//= require rails-ujs
//= require activestorage
//= require turbolinks
//= require_tree .
```

- require: 指定されたファイルを読み込む。
- require_tree: 指定されたディレクトリ以下のファイルを再帰的に読み込む。

指定はファイル名で行う。拡張子は省略する。`rails-ujs`、`activestorage`、`turbolinks`は assets 内に配置されていないが、デフォルトのの探索先は`app/assets`の他に`lib/assets`や`vendor/asets`があるため、そちらから読みだしている。

```css
/* app/assets/stylesheets/application.css */

@imprt "bootstrap";
@import "tasks";
```

### アセットの設定

- **config/initializers/assets.rb**

```ruby
# アセットのバージョン。バージョンを変更することでハッシュ値が代わりbrowserに強制的に読み込ませることができる
Rails.application.config.assets.version = '1.0'

# アセットパイプラインの探索先のパスを追加する
Rails.application.config.assts.paths << Rails.root.join('node_modules')

# application.js application.css と分けて precompile したいファイルを指定する
Railsapplication.config.assets.precompile += %w( admin.js admin.css)
```

- **config/environments/\***
  環境ごとの設定ファイル

```ruby
# 連結などの最適化を行わない。開発用オプション
config.assets.debug = true

# アセットへのリクエストをログに残さない。開発用オプション
config.assets.quiet = true

# css / js の圧縮方法を指定する
config.assets.js_compressor = :ugklifier
# config.assets.css_compressor = :sass

# 自動プリコンパイルをしないことでパフォーマンスを上げる。本番用オプション
config.assets.compile = false

# CDNなど、アセットファイルを配信するサーバーを設定する
# config.action_controller.asset_host = 'http://assets.example.com'
```

### 静的ファイルの発信サーバーを設定する

js、CSS、HTML などの静的ファイルをサーバーに任せ、`public`以下を無効化する。

```ruby
# config/environments/production.rb

config.public_file_server.enabled = ENV['RAILS_SERVE_STATIC_FILES'].present?
```

## Turbolinks

デフォルトで ON になっている高速化機能。ページ遷移せず、非同期通信で`BODY`タグだけ差し替える形で疑似遷移する。

- `HEAD`タグなどが維持されるので余計な通信が発生せず高速化する
- History API を活用しているので、ブラウザバックなども機能する
- 前回のキャッシュを先に表示し、通信完了後に画面を更新するプレビュー機能がある
- 最適化は`GET`リクエストのみだが、`POST`等の通信後のリダイレクトでも`turbolinks`による最適化が走る
- `window.onload`をはじめ、一部のイベントが機能しなくなる
- js オブジェクトがメモリ上に残りっぱなしになる。また、リロードされる可能性もあるので、残っていることを前提にすることもできない

turbolinks の恩恵を最大限受けるには、`HEAD`タグ内でリソースを読み込み、`BODY`タグ内では読み込みをしないようにする。  
また JS ファイルを`BODY`内に記述すると、以前の訪問時のオブジェクトがメモリ上に残っていたせいで、同じオブジェクトが二重に読み込まれる、といったバグの原因となる。

`window.onload`に代わるカスタムイベントが用意されている。

```js
document.addEventListener("turbolinks:load", () => {
  ...
});
```

## rails-ujs

非同期通信など、ビューヘルパと連携して開発体験を高めるライブラリ。

```slim
# in view template

= link_to '削除', task, method: :delete, remote: true, data: { confirm: "タスク「#{task.name}」}をさくじょします。よろしいですか？" }, class: 'btn-danger'
```

```html
<!-- in rendered html -->

<a
  data-confirm="タスク「...」}をさくじょします。よろしいですか？"
  class="btn btn-danger"
  data-remote="true"
  rel="nofollow"
  data-method="delete"
  href="/tasks/5"
>
  削除
</a>
```

ビュー内の`method`、`remote`、`confirm`が`data`属性になって描画される。これらの`data`属性は rails-ujs によって機能を付加される。

`remote` 属性によって AJAX 化された通信を JS でハンドリングするには`ajax:success`/`ajax:error`イベントを使う。

```js
element.addEventListener("ajax:success", function () {
  ...
});
```

また、サーバー側で レスポンスとして JS コードを返すことで、イベントハンドリングをすることなく処理を実行することもできる。（**SJR**）

```erb
# delete.js.erb

document.querySelector("#tasks-<%= @task..id %>").style.display = 'none';
```
