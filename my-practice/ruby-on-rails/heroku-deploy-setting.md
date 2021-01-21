# Capistrano によるデプロイ設定

学んだことを記録しておく。なお、今回はスピードを重視して Heroku へデプロイする。
参考 URL:

- ~~<https://qiita.com/tkykmw/items/a34441aae142e0e41b65>~~
- <https://qiita.com/poster-keisuke/items/f27e190e22d80dc254ed>
- <https://devcenter.heroku.com/articles/getting-started-with-rails6>

## production 設定

```shell
yarn install --ignore-engines
```

## Heroku 準備

- アカウント作成
- docker コンテナ内で heroku を使うので、docker コンテナに bash、curl、nodejs をインストールしておく。

```shell
curl https://cli-assets.heroku.com/install.sh | sh
heroku login
heroku create myclone-rails
git push heroku master
```

## DB を MySQL に変更

- アドオンを入れるためにクレジットカードを登録しておく  
  ignite プランなら無料

```shell
heroku addons:create cleardb:ignite
heroku config # creardb url を確認
heroku config:add DATABASE_URL='mysql2:{hogehoge}'
```

## MYSQL の SSL モードを OFF

- とても気に入らないが cleardb がエラーを吐くので MySQL の SSL を切る

```yml
# config/database.yml
production:
  <<: *default
  ssl_mode: :disabled
  sslverify: false
```

以上。MySQL の SSL モードを維持する方法を調査するのに死ぬほど苦労した。（そして解決できなかった。）
また Rails の Spring と spring-commands-rspec にとても苦しめられた。  
development / test 環境の GEM が production 環境でエラーを吐いてはいけないと思うのだが。  
binstub が spring をロードしようとしていることにもっと早く気付くべきだったか。  
いずれ Capistrano を使ったデプロイも試したい。
