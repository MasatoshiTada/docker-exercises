演習1 コンテナのライフサイクル
====================

# この演習の目的
WebサーバーNginxのコンテナを利用して、コンテナの作成・起動・停止・終了などのライフサイクルを体験します。

# 手順
## イメージのプル
(1) 次のコマンドで、Nginx 1.25のイメージをプルしてください。

```shell
$ docker image pull nginx:1.25
1.25: Pulling from library/nginx
f03b40093957: Pull complete 
eed12bbd6494: Pull complete 
fa7eb8c8eee8: Pull complete 
7ff3b2b12318: Pull complete 
0f67c7de5f2c: Pull complete 
831f51541d38: Pull complete 
Digest: sha256:af296b188c7b7df99ba960ca614439c99cb7cf252ed7bbc23e90cfda59092305
Status: Downloaded newer image for nginx:1.25
docker.io/library/nginx:1.25
```

(2) 次のコマンドで、Nginx 1.25のイメージがプルされたことを確認してください。

```shell
$ docker image ls
REPOSITORY                                        TAG       IMAGE ID       CREATED         SIZE
nginx                                             1.25      f9c14fe76d50   8 days ago      143MB
...
```

> 明示的にイメージのプルをすることはほとんどありません。コンテナ作成（次で説明）の際、ローカルにイメージが存在しなければ自動的にプルされるからです。
> しかし今回は、コンテナのライフサイクルを学ぶために敢えて明示的なプルを行っています。

## コンテナの作成
(1) 次のコマンドで、①Nginx 1.25のコンテナを作成、②ホストのポート番号8080とコンテナのポート番号80をマッピング、を行ってください。

```shell
$ docker container create -p 8080:80 nginx:1.25
baec1d64c594aeb93e02f04aa5fa502d0b9d040730826edfff26af649aa7e1c8
```

(2) 次のコマンドで、コンテナが作成されたことを確認してください。併せて、コンテナIDをメモしてください。

```shell
$ docker container ls -a
CONTAINER ID   IMAGE           COMMAND                   CREATED              STATUS       PORTS     NAMES
baec1d64c594   nginx:1.25      "/docker-entrypoint.…"    About a minute ago   Created                stoic_edison
...
```

(3) 次のコマンドで、まだコンテナが起動していないことを確認してください。

```shell
$ docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## コンテナの起動
(1) 次のコマンドで、作成したコンテナを起動してください（コンテナIDは先ほどメモしたものに置き換えてください）。

```shell
$ docker container start baec1d64c594
baec1d64c594
```

(2) 次のコマンドで、コンテナが起動したことを確認してください。

```shell
$ docker container ls 
CONTAINER ID   IMAGE        COMMAND                  CREATED         STATUS         PORTS                                   NAMES
baec1d64c594   nginx:1.25   "/docker-entrypoint.…"   3 minutes ago   Up 5 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   stoic_edison
```

(3) ブラウザで http://localhost:8080 にアクセスしてください。Nginxのトップページが表示されれば成功です。なお、このページは後の手順でも使いますので、開いたままにしておいてください。

## コンテナの停止
(1) 次のコマンドで、コンテナを停止してください。

```shell
$ docker container stop baec1d64c594 
baec1d64c594
```

(2) 次のコマンドで、コンテナが停止していることを確認してください。

```shell
$ docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

(3) 次のコマンドで、コンテナがまだ存在していることを確認してください。

```shell
$ docker container ls -a
CONTAINER ID   IMAGE             COMMAND                   CREATED         STATUS                       PORTS       NAMES
baec1d64c594   nginx:1.25        "/docker-entrypoint.…"    6 minutes ago   Exited (137) 7 seconds ago               stoic_edison
```

(4) ブラウザで再読み込みしてください。アクセスできない旨が表示されるはずです。これは、コンテナが停止したためです。

> 停止したコンテナは`docker container start <コンテナID>`で再び起動できます。

## コンテナの削除
(1) 次のコマンドで、コンテナを削除してください。

```shell
$ docker container rm baec1d64c594
baec1d64c594
```

> まだコンテナが停止していない状態で削除しようとするとエラーになります。

(2) 次のコマンドで、コンテナが削除されたことを確認してください。

```shell
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## docker container runコマンドの利用
`docker container run`コマンドは、コンテナの作成および起動を行います。すなわち、`docker container create`および`docker container start`と同等の機能を持ちます。

(1) 次のコマンドで、Nginx 1.25コンテナの作成およ起動を行ってください。

```shell
$ docker container run -d --rm -p 8080:80 nginx:1.25
858111422f5101fa7ce5dd983d8561c5f4c2c195365f59aa7bc83b319c51d0e6
```

> - `-d`オプションは、このNginxコンテナがバックグラウンドで動くことを示します。このオプションが無い場合、コマンドを実行したターミナルにNginxのログが表示されます。コンテナの停止はCtrl+Cで行います。
> - `--rm`オプションは、このNginxコンテナが停止した際に削除されることを示します。このオプションが無い場合、コンテナを停止しても削除がされませんので、注意してください。

(2) 次のコマンドで、コンテナが起動したことを確認してください。

```shell
$ docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS                                   NAMES
858111422f51   nginx:1.25   "/docker-entrypoint.…"   15 seconds ago   Up 12 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   compassionate_chebyshev
```

(3) ブラウザで http://localhost:8080 にアクセスしてください。Nginxのトップページが表示されれば成功です。

(4) 次のコマンドで、コンテナを停止してください。

```shell
$ docker container stop 858111422f51 
858111422f51
```

(5) 次のコマンドで、コンテナが削除されたことを確認してください。

```shell
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
