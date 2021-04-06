演習1 コンテナのライフサイクル
====================

# この演習の目的
WebサーバーNginxのコンテナを利用して、コンテナの作成・起動・停止・終了などのライフサイクルを体験します。

# 手順
## コンテナの作成
(1) 次のコマンドで、①Nginx 1.19のコンテナを作成、②ホストのポート番号8080とコンテナのポート番号80をマッピング、を行ってください。

```bash
$ docker container create -p 8080:80 nginx:1.19
Unable to find image 'nginx:1.19' locally
1.19: Pulling from library/nginx
6fcf2156bc23: Pull complete 
31ca62f63eaf: Pull complete 
1cd5998f3010: Pull complete 
b2dd8627b82f: Pull complete 
c4d503e83445: Pull complete 
036b0382d2d7: Pull complete 
Digest: sha256:bae781e7f518e0fb02245140c97e6ddc9f5fcf6aecc043dd9d17e33aec81c832
Status: Downloaded newer image for nginx:1.19
e026f95ec496f80bbe43022bdeeeeb4711c58bf104a465630c6a10315be317b4
```

(2) 次のコマンドで、コンテナが作成されたことを確認してください。併せて、コンテナIDをメモしてください。

```bash
$ docker container ls -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                         PORTS     NAMES
e026f95ec496   nginx:1.19     "/docker-entrypoint.…"   2 minutes ago       Created                                  vibrant_jang
```

(2) 次のコマンドで、コンテナが作成されたことを確認してください。併せて、コンテナIDをメモしてください。

```bash
$ docker container ls -a
CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS                         PORTS     NAMES
e026f95ec496   nginx:1.19     "/docker-entrypoint.…"   2 minutes ago       Created                                  vibrant_jang
```

(3) 次のコマンドで、まだコンテナが起動していないことを確認してください。

```bash
$ docker container ls 
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

## コンテナの起動
(1) 次のコマンドで、作成したコンテナを起動してください（コンテナIDは先ほどメモしたものに置き換えてください）。

```bash
$ docker container start e026f95ec496
e026f95ec496
```

(2) ブラウザで http://localhost:8080 にアクセスしてください。Nginxのトップページが表示されれば成功です。

(3) 次のコマンドで、コンテナが起動したことを確認してください。

```bash
$ docker container ls 
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS         PORTS                  NAMES
e026f95ec496   nginx:1.19   "/docker-entrypoint.…"   3 minutes ago    Up 1 minutes   0.0.0.0:8080->80/tcp   vibrant_jang
```

## コンテナの一時停止・再開
(1) 次のコマンドで、コンテナ内の全プロセスを一時停止してください。

```bash
$ docker container pause e026f95ec496
e026f95ec496
```

(2) 次のコマンドで、コンテナ内のプロセスが一時停止状態（ `Paused` ）であることを確認してください。

```bash
$ docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS                  PORTS                  NAMES
e026f95ec496   nginx:1.19   "/docker-entrypoint.…"   13 minutes ago   Up 5 minutes (Paused)   0.0.0.0:8080->80/tcp   vibrant_jang
```

(3) ブラウザで再読み込みしてください。再読み込みがいつまで経っても終わらないはずです。これは、コンテナ内のNginxプロセスが一時停止中であるためです。

(4) 次のコマンドで、コンテナ内のプロセスを再開してください。

```bash
$ docker container unpause e026f95ec496
e026f95ec496
```

(5) 次のコマンドで、コンテナが起動状態であることを確認してください。

```bash
$ docker container ls
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS         PORTS                  NAMES
e026f95ec496   nginx:1.19   "/docker-entrypoint.…"   17 minutes ago   Up 9 minutes   0.0.0.0:8080->80/tcp   vibrant_jang
```

(6) ブラウザで再読み込みしてください。再読み込みがすぐに終わるはずです。これは、コンテナ内のNginxプロセスが起動中になったためです。

## コンテナの停止
(1) 次のコマンドで、コンテナを停止してください。

```bash
$ docker container stop e026f95ec496 
e026f95ec496
```

(2) 次のコマンドで、コンテナが停止していることを確認してください。

```bash
$ docker container ls                 
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

(3) 次のコマンドで、コンテナがまだ存在していることを確認してください。

```bash
$ docker container ls -a
CONTAINER ID   IMAGE        COMMAND                  CREATED             STATUS                      PORTS     NAMES
e026f95ec496   nginx:1.19   "/docker-entrypoint.…"   34 minutes ago      Exited (0) 2 minutes ago              vibrant_jang
```

(4) ブラウザで再読み込みしてください。アクセスできない旨が表示されるはずです。これは、コンテナが停止したためです。

## コンテナの削除
(1) 次のコマンドで、コンテナを削除してください。

```bash
$ docker container rm e026f95ec496          
e026f95ec496
```

(3) 次のコマンドで、コンテナが削除されたことを確認してください。

```bash
$ docker container ls -a
CONTAINER ID   IMAGE        COMMAND                  CREATED             STATUS                      PORTS     NAMES
```
