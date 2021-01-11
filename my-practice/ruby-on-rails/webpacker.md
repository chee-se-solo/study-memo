# Webpacker 覚書

フロントエンド系のツール類に関して全くの初心者だったがこの機会に入門したので、webpacker を使ってみた結果をまとめていく。  
正直なところ、webpacker を使うくらいなら webpack でいい気がする。  
ウェブ上のドキュメントの量がまるで違う。困ったときに素早く解決できるのは webpack のほう。  
フロントエンド入門者は webpack 入門の記述を使えず余計に困る。  
そして webpacker で何ができるのかを調べることは、そのまま webpack でできることを調べることになる。  
webpack に関する知識０では使えず、webpack を使える人間にとっては webpacker は余計なお世話らしい。  
どういう層を対象にしているんだ webpacker。

## webpacker インストール

## webpack-dev-server 設定

## webpack on Docker 設定

## webpacker の構成

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
  画像ファイルなど性的ファイル。ビューからは src 属性に`asset_pack_path`ヘルパを使ってアクセスする。`image_pack_tag`でもよいらしい。

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
