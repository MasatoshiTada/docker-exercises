演習2 コンテナイメージのビルド
====================

# この演習の目的
Nginxのコンテナイメージを自分で作成することで、Dockerfileの書き方やコンテナイメージの作成方法を理解します。

通常はNginxがインストール済みのコンテナイメージを利用しますが、今回は練習のためにUbuntuにNginxを自分でインストールしてみます。また、デフォルトで表示されるindex.htmlを差し替えます。

本演習のコマンドは、すべてex02フォルダで実行してください。

```bash
$ cd ex02
```

# 手順
## Dockerfileの作成
(1) エディタなどでDockerfileの内容を確認してください。

```Dockerfile:Dockerfile
# ベースイメージはUbuntu 20.04
FROM ubuntu:20.04

# タイムゾーンを環境変数で指定
ENV TZ=Asia/Tokyo

    # タイムゾーンを日本に設定
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone \
    # インストール可能なパッケージ一覧を更新
    && apt update \
    # Nginxをインストール
    && apt install -y nginx

# HTMLファイルをコピー
COPY ./contents /var/www/html

# ポート番号80を公開
EXPOSE 80

# コンテナ起動時にNginxを起動させる
CMD ["nginx", "-g", "daemon off;"]
```

## コンテナイメージのビルド
(1) 次のコマンドで、コンテナイメージをビルドしてください。

```bash:build.sh
$ docker image build -t my-nginx:0.0.1 .
[+] Building 0.1s (8/8) FINISHED                                                                                                    
 => [internal] load build definition from Dockerfile                                                                           0.0s
 => => transferring dockerfile: 636B                                                                                           0.0s
 => [internal] load .dockerignore                                                                                              0.0s
 => => transferring context: 2B                                                                                                0.0s
 => [internal] load metadata for docker.io/library/ubuntu:20.04                                                                0.0s
 => [internal] load build context                                                                                              0.0s
 => => transferring context: 69B                                                                                               0.0s
 => [1/3] FROM docker.io/library/ubuntu:20.04                                                                                  0.0s
 => CACHED [2/3] RUN ln -snf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime     && echo Asia/Tokyo > /etc/timezone     && apt   0.0s
 => CACHED [3/3] COPY ./contents /var/www/html                                                                                 0.0s
 => exporting to image                                                                                                         0.0s
 => => exporting layers                                                                                                        0.0s
 => => writing image sha256:4ef5d7a3c65cebf632bf1641d1af025a07296aff6135d368d85f496e68e955c7                                   0.0s
 => => naming to docker.io/library/my-nginx:0.0.1                                                                              0.0s
```

## コンテナの起動
(1) 次のコマンドで、コンテナを起動してください。

```bash:run.sh
$ docker container run --rm -p 8080:80 my-nginx:0.0.1
```

(2) ブラウザで http://localhost:8080 にアクセスしてください。サンプルページが表示されれば成功です。
