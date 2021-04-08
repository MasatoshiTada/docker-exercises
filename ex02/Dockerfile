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
