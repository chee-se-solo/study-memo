# Webpacker 覚書

フロントエンド系のツール類に関して全くの初心者だったがこの機会に入門したので、webpacker を使ってみた結果をまとめていく。  
正直なところ、webpacker を使うくらいなら webpack でいい気がする。  
ウェブ上のドキュメントの量がまるで違う。困ったときに素早く解決できるのは webpack のほう。  
フロントエンド入門者は webpack 入門の記述を使えず余計に困る。  
そして webpacker で何ができるのかを調べることは、そのまま webpack でできることを調べることになる。  
webpack に関する知識０では使えず、webpack を使える人間にとっては webpacker は余計なお世話らしい。  
どういう層を対象にしているんだ webpacker。

## webpacker インストール

```shell
bundle install
yarn install
bin/rails webpacker:install
```

Rails6 なら Gemfile にはデフォルトで入っている。`bundle install`後に webpacker をインストールする。

## webpack-dev-server 設定

- **host**  
  Rails から webpack-dev-server へアクセスする際のホスト名

- **public**  
  ブラウザから dev-server にアクセスする際の公開 URL。80 ポートでない場合はポート番号もここで指定する。

- **hmr**  
  Hot Module Replacement。JS や CSS を書き換えたときにブラウザをリロードすることなく変更を反映させる。ブラウザと dev-server は websocket を使って常に通信しており、変更を検知すると dev-server は新しいモジュールをブラウザに配布する。この通信のために dev-server は 3035 ポートを使用している。

- **inline**  
  hmr の有効化に必要な設定。js などのリソースをインラインに展開して websocket 通信で受け取った平文で書き換えるなどしているのだろうか？要調査。

- **disable_host_check**
  不明。おそらく websocket 通信をするときに、クロスオリジンの通信を許可するヘッダを設定する項目、なのではなかろうか。要調査。

### 起動コマンド

```shell
bin/webpack-dev-server
```

## webpack on Docker 設定

- host: webpack（webpack-dev-server サービスホスト名）
- public: 0.0.0.0:3035

また、以下の環境変数を設定する

- WEBPACKER_DEV_SERVER_HOST=0.0.0.0（webpack-dev-server コンテナ）
- WEBPACKER_DEV_SERVER_HOST=webpack（rails app コンテナ）

HMR は Rails、dev-server、ブラウザの 3 ノードでトライアングルのネットワークを構成する。

- Rails は dev-server に TCP 通信で起動確認をし、起動している場合はコンパイルと委譲する。
- dev-server はブラウザと通信し、hmr のためにリアルタイム通信を行う。
- ブラウザは Rails からコンテンツを受け取り、静的リソースなどを dev-server から受け取る。

このため、それぞれのノードからの参照名、使用ポートを把握して適切に割り当てる必要がある。  
とくに dev-server の hostname はブラウザと Rails で違うので注意。また、dev-server 用のポートを開けるのも忘れてはいけない。  
また、Docker コンテナ内では、ファイルシステムの違いの都合上、ファイル変更イベントが検知できない。  
そのためファイル変更の検知方法をデフォルトからポーリング方式に変更する必要がある。

### 設定ファイル

- config/webpack.yml  
  yml で設定できる。

- config/webpack/enviroment.js  
  webpack の細かな設定ができる。それぞれの環境毎に設定ファイルがわけられている。

### ソースファイル

- app/javascript/packs  
   webpacker のエントリーポイントを入れるディレクトリ。（`application.js`および`application.css`）

- app/javascript/src  
  個別に分割した javascript ファイル。`javascript_packs_with_chunks_tag`で読みだす。

- app/javascript/stylesheets  
  個別に分割した css ファイル。`stylesheet_packs_with_cunks_tag`で読みだす。

- app/javascript/images  
  画像ファイルなど静的ファイル。ビューからは src 属性に`asset_pack_path`ヘルパを使ってアクセスする。`image_pack_tag`でもよいらしい。

## TypeScript 導入

## Vue / React 導入

## CSS フレームワーク導入

material-design-icons-icon を導入した。  
`css-loader`、`sass-loader`は最初から組み込まれているようだ。

```shell
# アセットコンパイルからCSSを削除
rm -rf app/assets/stylesheets

# CSSフレームワークを導入し、webpackerへ追加
yarn add material-design-icons-icon
touch app/javascript/packs/application.css
echo '@import "~material-design-icons/iconfont/material-design-icons";' >> app/javascript/packs/application.css
```

### resolve-url-loader を追加する。

application.css を読みだすと、フォントファイルが見つからずエラーが起きる。相対パスで指定されているフォントファイルを正しくロードさせるために、resolve-url-loader を追加する。

```shell
yarn add resolve-url-loader
```

```javascript
// config/webpack/environment.js
const { environment } = require("@rails/webpacker");
const resolveUrlLoader = require("./loaders/resolve-url-loader");

// Enable css modules with sass loader
environment.loaders.get("sass").use.splice(-1, 0, {
  loader: "resolve-url-loader",
  options: {
    attempts: 1,
  },
});

module.exports = environment;
```

うまく動かない。調べてみたところ、resolve-url-loader の前の loader で souceMap を有効にしておく必要がある模様。  
<https://www.npmjs.com/package/resolve-url-loader#configure-webpack>  
webpacker で resolve-url-loader を組み込むのは隠蔽されている設定を開封して差し込む必要があるので、正攻法ではないと判断してあきらめる。~~やっぱり webpacker ダメでは？~~

### webpacker に解決させるのをあきらめる

```css
@import "https://fonts.googleapis.com/icon?family=Material+Icons";
```

~~こんなのあんまりだ。~~
解決。
