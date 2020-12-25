# Docker 覚書

## Dockerfile

基本。Docker Hub からイメージを持ってきてコマンドを実行したりファイルをコピーして配置したりする。

## docker-compose.yml

複数の Docker コンテナを連結させて稼働させるときに使う便利な設定ファイル。
Docker コンテナは１つにつき 1 プロセスが基本なので、APP コンテナ、DB コンテナに別れたり、さらに WEB コンテナが追加されたりする。
コンテナの依存関係を記述したり、コンテナ名とは別にサービス名を付けて管理できたりする。
ホストとのマウント設定も記述できるので、１つのコンテナだけでも起動時のオプションを省略するために docker-compose.yml を使うこともできる

## Dockerfile 基本コマンド

### FROM

Docker イメージの指定。

### RUN

コマンドの実行。Docker は実行結果をキャッシュするのと、Dockerfile の命令の数だけレイヤーを作って状態を管理しているので
RUN で実行するコマンドは、でできるだけ && で結んで一回で済むように作る。

### WORKDIR

作業ディレクトリ指定。シェルでいうところの cd コマンド。
これの後に相対パスを記述した場合、ここで指定したディレクトリからのパスになる。

### COPY

ファイルのコピー。コンテナ―コンテナ間やホスト－コンテナ間でもファイルの受け渡しができるのが、RUN cp との違い。

### EXPOSE

コンテナ内で Listen するポートの指定。このポートをコンテナ外に公開するわけではない。
例えば、ホストの 63306 番ポートとコンテナの 3306 番ポートをつなげると、ホストの 63306 番ポートにアクセスすることでコンテナの MySQL データベースにアクセスしたりできる。

### ADD

なんかいろいろできるらしい。詳しく調べていない。
COPY コマンドと同じことができる上位互換コマンド。何をしているのかわかりにくいので、COPY コマンドで足りるときは COPY コマンドで済ませることが推奨される。

### ENV

環境変数の設定。

### CMD

コマンド・・・のように見せかけてデフォルト引数の設定。詳しくは後述。

### ENTRYPOINT

Docker コンテナを立ち上げたときの実行プロセス。entrypoint.sh などのシェルを指定することで特定処理を実行させつつ、CMD や入力のコマンドを実行させることもできる。詳しくは後述。

## docker-compose.yml 基本設定

これを見れば大体わかる。

- https://qiita.com/zembutsu/items/9e9d80e05e36e882caaa
- https://docs.docker.com/compose/compose-file/

### version

Docker Engine と対応した docker-compose ファイルのバージョン。今なら 3 を指定しておけば間違いない。

### services

docker-compose で管理するコンテナ。

### services - (サービス名)

コンテナ名とは別につけるコンテナの名前。docker コマンドはコンテナ名を指定して使うが、docker-compose コマンドはサービス名を指定して使う。

### services - (サービス名) - image

コンテナのベースイメージ。Dockerfile でいうところの FROM

### services - (サービス名) - build

Dockerfile へのパスを指定する。その Dockerfile を使ってイメージをビルドする。
Dockerfile の名前を指定する場合は build でははく、以下の context と dockerfile を使って指定する。

### services - (サービス名) - build - dockerfile

Dockerfile 名の指定。Dockerfile 以外の名前を使いたいとき。

### services - (サービス名) - build - context

dockerfile が配置されている階層へのパス。Dockerfile 以外の名前の Dockerfile を使いたいとき。

### services - (サービス名) - environment

環境変数設定。Dockerfile でいうところの ENV

### services - (サービス名) - ports

ポート指定。Dockerfile でいうところの EXPOSE に近い。63306:3306 とかで公開するポート(63306)とコンテナ内で Listen するポート(3306)の組み合わせを指定できる。

### services - (サービス名) - depends_on

依存サービス名の指定。APP コンテナの場合、DB コンテナに依存しているのでこれを指定する。

### services - (サービス名) - volumes

マウントするボリュームを指定できる。.:/path/to/などの指定で、docker-compose.yml があるフォルダが、コンテナ内の/path/to にマウントされる。  
また、ボリューム名:/path/to などで、ルートルート階層の volumes で定義したボリュームをマウントすることもできる。volumes で定義した volume は複数のサービスコンテナで共有したりできる。
コンテナを削除しても volumes で定義したボリュームは消えない（と思う）

### services - (サービス名) - stdin_oepn

端末（tty）からの入力を受け付ける。コマンドの -i オプションに相当。

### services - (サービス名) - tty

端末を受け付けるというかなんというか。ssh でおなじみのアレ。コマンドの -t 相当する。
Docker コンテナはポートを Listen するプロセスが生きているか、このオプションが true でないと起動後にすぐ終了してしまうので注意。

### services - (サービス名) - entrypoint

Dockerfile でいう ENTRYPOINT。

### services - (サービス名) - command

Dockerfile でいう CMD。

## ENTRYPOINT と CMD

Docker コンテナで実行するプロセス指定と追加引数の関係。
ここを読めばわかる。
https://qiita.com/uehaj/items/e6dd013e28593c26372d

ENTRYPOINT が docker run するときのプロセスで、CMD が追加で指定できる引数。  
乱暴に説明すれば、ENTRYPOINT がコマンドで CDM が追加引数。ただし、ENTRYPOINT がないときは CMD にコマンドを設定できる。  
さらに乱暴に言えば、Docker run した際に、[ENTRYPOINT] [CMD] の形で実行されると思っていてもいい。  
ENTRYPOINT と CMD の設定の仕方次第で当てはまらない挙動も普通にあるが、細かい挙動はあまり統一性がない。  
詳細はこっち。  
http://docs.docker.jp/v17.06/engine/reference/builder.html#cmd-entrypoint

ENTRYPOINT にシェルを指定して、exec "$@"の形で終わらせることで、ENTRYPOINT のシェルで特定の処理をさせつつ、最後に CMD のコマンドを実行する。という挙動ができる。
具体的には entrypoint.sh 内で、pid ファイルなどの動作に関わるファイルを削除し、CMD でサーバーを起動するなど。
entrykit とかいう定番があるらしいので、そっちもできることをまとめておきたい。あとは、今は開発環境を作るために Docker を使ってるけど、いずれ Twelve Factor App とか理解したい。
