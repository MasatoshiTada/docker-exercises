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
## コンテナイメージのビルド
(1) エディタなどでDockerfileの内容を確認してください。

```Dockerfile
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

(2) 次のコマンドで、コンテナイメージをビルドしてください（build.shに同じコマンドが書いてあります）。

```bash
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

(3) 次のコマンドで、コンテナイメージが作成されたことを確認してください。

```bash
$ docker image ls
REPOSITORY                       TAG                       IMAGE ID       CREATED          SIZE
my-nginx                         0.0.1                     4ef5d7a3c65c    1 minutes ago   149MB
```

## コンテナの起動
(1) 次のコマンドで、コンテナを起動してください（run.shに同じコマンドが書いてあります）。

```bash
$ docker container run --rm -p 8080:80 my-nginx:0.0.1
```

(2) ブラウザで http://localhost:8080 にアクセスしてください。サンプルページが表示されれば成功です。

(3) ターミナルでCtrl+Cを押下してください。コンテナが停止します。

## コンテナイメージのアップロード
(1) まだDocker Hubのアカウントを持っていない場合は、 https://hub.docker.com/ から作成してください。

(2) 次のコマンドで、Docker Hubにログインしてください。 `xxxxxxxx` の部分は自分のDocker Hubユーザー名を入力してください。

```bash
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xxxxxxxx
Password: 
Login Succeeded
```

(3) 次のコマンドで、コンテナイメージに自分のユーザー名を含むタグを付加してください。

```bash
$ docker tag my-nginx:0.0.1 xxxxxxxx/my-nginx:0.0.1
```

(4) 次のコマンドで、先ほど付加したタグを確認してください。コンテナイメージが2つあるように見えますが、1つのイメージに2つのタグが付加されているだけです（ `IMAGE ID` が同じであることから、同一のイメージであることが分かります）。

```bash
$ docker image ls
REPOSITORY                       TAG                       IMAGE ID       CREATED          SIZE
my-nginx                         0.0.1                     4ef5d7a3c65c   27 minutes ago   149MB
xxxxxxxx/my-nginx                0.0.1                     4ef5d7a3c65c   27 minutes ago   149MB
```

(5) 次のコマンドで、コンテナイメージをDocker Hubにアップロードしてください。

```bash
$ docker image push xxxxxxxx/my-nginx:0.0.1        
The push refers to repository [docker.io/xxxxxxxx/my-nginx]
3017b72d93d7: Pushed 
69b4b94864b7: Pushed 
7137a365e1e4: Pushed 
9c64db1afb9e: Pushed 
8d4eb2300df0: Pushed 
0.0.1: digest: sha256:d1be392b494958959d11189adbe3a1e08bfdd8d6be3eccd70ef9f1df9ace8b20 size: 1362
```

(6) ブラウザで https://hub.docker.com/ にアクセスしてください。コンテナイメージ一覧の中にmy-nginxが確認できるはずです。
