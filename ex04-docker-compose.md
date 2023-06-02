演習4 Docker Compose
====================

# この演習の目的
Docker Composeで複数のコンテナを起動します。

本演習のコマンドは、すべてex03フォルダで実行してください。

```shell
$ cd ex04
```

# 手順
## Docker Composeによる複数コンテナの起動
(1) エディタなどで[docker-compose.yaml](ex04/docker-compose.yaml)の内容を確認してください。

```yaml
services:
  web:
    container_name: web-server
    image: nginx:1.25
    ports:
      - "8080:80"
  db:
    container_name: db-server
    image: postgres:15.3
    ports:
      - "5555:5432"
    # 環境変数の設定 (https://hub.docker.com/_/postgres)
    environment:  # 環境変数の設定
      - POSTGRES_DB=sample
      - POSTGRES_USER=sample
      - POSTGRES_PASSWORD=sample
```

(2) 次のコマンドでコンテナを起動してください。

```shell
$ docker compose up -d
[+] Running 3/3
 ⠿ Network ex04_default  Created     0.1s
 ⠿ Container web-server  Started     0.7s
 ⠿ Container db-server   Started     0.7s
```

> `-d`オプションは、コンテナをバックグラウンドで動くよう指定します。

(3) 次のコマンドで、NginxとPostgresのコンテナが起動していることを確認してください。

```shell
$ docker container ls
CONTAINER ID   IMAGE           COMMAND                  CREATED              STATUS              PORTS                                       NAMES
182b43256c25   nginx:1.25      "/docker-entrypoint.…"   About a minute ago   Up About a minute   0.0.0.0:8080->80/tcp, :::8080->80/tcp       web-server
f2b3a69eef92   postgres:15.3   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:5555->5432/tcp, :::5555->5432/tcp   db-server
```

## Docker Composeによる複数コンテナの停止
(1) 次のコマンドでコンテナを停止してください。

```shell
$ docker compose down -v
[+] Running 3/3
 ⠿ Container web-server  Removed    0.6s
 ⠿ Container db-server   Removed    0.5s
 ⠿ Network ex04_default  Removed    0.2s
```

> `-v`オプションは、コンテナのデータを保存しているボリュームを削除することを示します。

(2) 次のコマンドで、コンテナが停止＋削除されたことを確認してください。

```shell
$ docker container ls -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```
