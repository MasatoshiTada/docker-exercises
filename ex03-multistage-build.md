演習3 マルチステージビルド
====================

# この演習の目的
マルチステージビルドを利用して、Java（Spring Boot）アプリケーションのビルドおよびコンテナ化を行います。

本演習のコマンドは、すべてex03フォルダで実行してください。

```bash
$ cd ex03
```

# 手順
## マルチステージビルド
(1) エディタなどでDockerfileの内容を確認してください。

```Dockerfile
#
# for build
#
FROM maven:3.6.0-jdk-11 AS build
COPY . /home/maven
WORKDIR /home/maven
RUN mvn package

#
# for packaging container image
#
FROM openjdk:11-jdk
COPY --from=build /home/maven/target/*.jar /srv/application.jar
RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot
USER springboot
EXPOSE 8080
CMD ["java", "-jar", "/srv/application.jar"]
```

(2) 次のコマンドで、コンテナイメージをビルドしてください（build.shに同じコマンドが書いてあります）。

```bash
$ docker image build -t spring-boot-sample:0.0.1 .
[+] Building 854.3s (15/15) FINISHED                                                                                                
 => [internal] load build definition from Dockerfile                                                                           0.0s
 => => transferring dockerfile: 37B                                                                                            0.0s
 => [internal] load .dockerignore                                                                                              0.0s
 => => transferring context: 2B                                                                                                0.0s
 => [internal] load metadata for docker.io/library/openjdk:11-jdk                                                              3.3s
 => [internal] load metadata for docker.io/library/maven:3.6.0-jdk-11                                                          4.2s
 => [auth] library/openjdk:pull token for registry-1.docker.io                                                                 0.0s
 => [auth] library/maven:pull token for registry-1.docker.io                                                                   0.0s
 => [internal] load build context                                                                                              0.0s
 => => transferring context: 2.83kB                                                                                            0.0s
 => CACHED [stage-1 1/3] FROM docker.io/library/openjdk:11-jdk@sha256:d7d9f9fb02ad2b196815dfb6d3a36e23b4cae8cf9dc27828d9d4479  0.0s
 => [build 1/4] FROM docker.io/library/maven:3.6.0-jdk-11@sha256:6a0430ded2cfaba7e16080f4cc097c9c65d1406b3b235d0fcfcfd84c35  731.9s
 => => resolve docker.io/library/maven:3.6.0-jdk-11@sha256:6a0430ded2cfaba7e16080f4cc097c9c65d1406b3b235d0fcfcfd84c354c4177    0.0s
 => => sha256:6dca82770cb3702e9c92d2f155923c0fb55671c1c373b8b50640b7bf05ed8418 4.09MB / 4.09MB                                 1.5s
 => => sha256:5385e634d2a1a330aea59f510b0676b269167a579dddb586b27917d80d0dfd01 43.13MB / 43.13MB                               7.4s
 => => sha256:6a0430ded2cfaba7e16080f4cc097c9c65d1406b3b235d0fcfcfd84c354c4177 2.37kB / 2.37kB                                 0.0s
 => => sha256:20ec4d5943e939a7350d16dced4200d56a736f63847ad6e7666b7d997109ad50 3.04kB / 3.04kB                                 0.0s
 => => sha256:964512e93d51cacaa64387aa885fe355bf97988c64aac6801a9d1f61d577513f 8.72kB / 8.72kB                                 0.0s
 => => sha256:6696bae2f570e0c96dcb21308e525216796d3452d1a9b8dcaba168ba37f3f464 9.73MB / 9.73MB                                 1.6s
 => => sha256:d95e88827e80fd1e7c7d0a78bc734615833dd6ac19b07c1ad3b8b1c837bb4ece 48.01MB / 48.01MB                               8.6s
 => => sha256:b918968c658e5fccf315dfefd0fa9aa78bcd41675036298167da2bec4b1406dd 877.63kB / 877.63kB                             4.4s
 => => sha256:e97cc37ba01632d37959a7e25eebbdfe5197bf64ca8acc15024f206da5279532 224B / 224B                                     5.9s
 => => sha256:4b0708dcd7a8392d5757aaa0e78df5dc4e065be9267862f497b3121ccfd52183 248B / 248B                                     7.0s
 => => sha256:bf1429ad4df1b7d82c847322c9f6bdab347c2701b909c3ca10ff17f766560743 131B / 131B                                     8.2s
 => => sha256:ebc32a56f0121da893eba0768be7d26b89898e0b531f82b7acc359ce84fb25df 296.53MB / 296.53MB                           727.0s
 => => extracting sha256:5385e634d2a1a330aea59f510b0676b269167a579dddb586b27917d80d0dfd01                                      1.1s
 => => sha256:4a28010560a88d3513d3041cc69057a9b23e6d6042202d27ebc9dce42af0962a 225B / 225B                                     9.5s
 => => extracting sha256:6696bae2f570e0c96dcb21308e525216796d3452d1a9b8dcaba168ba37f3f464                                      0.2s
 => => sha256:ae67448437f46aea021736722f2348e232f00d5b23325d7eeb8637bef471c0e9 9.09MB / 9.09MB                                 9.6s
 => => extracting sha256:6dca82770cb3702e9c92d2f155923c0fb55671c1c373b8b50640b7bf05ed8418                                      0.1s
 => => extracting sha256:d95e88827e80fd1e7c7d0a78bc734615833dd6ac19b07c1ad3b8b1c837bb4ece                                      1.4s
 => => sha256:71579a4f9c8ed653d4907aeff625acba79ce0467a726c336efcd5a3551f2168b 752B / 752B                                    10.5s
 => => sha256:1f9dd7bca19980e1757c0920146da8984f5a2df1242e539a89f986d3f41deead 361B / 361B                                    10.0s
 => => extracting sha256:b918968c658e5fccf315dfefd0fa9aa78bcd41675036298167da2bec4b1406dd                                      0.1s
 => => extracting sha256:e97cc37ba01632d37959a7e25eebbdfe5197bf64ca8acc15024f206da5279532                                      0.0s
 => => extracting sha256:4b0708dcd7a8392d5757aaa0e78df5dc4e065be9267862f497b3121ccfd52183                                      0.0s
 => => extracting sha256:bf1429ad4df1b7d82c847322c9f6bdab347c2701b909c3ca10ff17f766560743                                      0.0s
 => => extracting sha256:ebc32a56f0121da893eba0768be7d26b89898e0b531f82b7acc359ce84fb25df                                      4.0s
 => => extracting sha256:4a28010560a88d3513d3041cc69057a9b23e6d6042202d27ebc9dce42af0962a                                      0.0s
 => => extracting sha256:ae67448437f46aea021736722f2348e232f00d5b23325d7eeb8637bef471c0e9                                      0.1s
 => => extracting sha256:71579a4f9c8ed653d4907aeff625acba79ce0467a726c336efcd5a3551f2168b                                      0.0s
 => => extracting sha256:1f9dd7bca19980e1757c0920146da8984f5a2df1242e539a89f986d3f41deead                                      0.0s
 => [build 2/4] COPY . /home/maven                                                                                             0.0s
 => [build 3/4] WORKDIR /home/maven                                                                                            0.0s
 => [build 4/4] RUN mvn package                                                                                              117.4s
 => [stage-1 2/3] COPY --from=build /home/maven/target/*.jar /srv/application.jar                                              0.0s
 => [stage-1 3/3] RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot                              0.2s
 => exporting to image                                                                                                         0.1s
 => => exporting layers                                                                                                        0.0s
 => => writing image sha256:616af77ebd5e7574655bb59f3a0f906d2f077a497899cfbd1109dee7305ab951                                   0.0s
 => => naming to docker.io/library/spring-boot-sample:0.0.1 
```

(3) 次のコマンドで、コンテナイメージが作成されたことを確認してください。

```bash
$ docker image ls
REPOSITORY                                                                    TAG                                                     IMAGE ID       CREATED              SIZE
spring-boot-sample                                                            0.0.1                                                   616af77ebd5e   About a minute ago   655MB
```

## コンテナの起動
(1) 次のコマンドで、コンテナを起動してください（run.shに同じコマンドが書いてあります）。

```bash
$ docker container run --rm -p 8080:80 spring-boot-sample:0.0.1
```

(2) ブラウザで http://localhost:8080 にアクセスしてください。「Spring Boot Sample」と表示されれば成功です。

(3) ターミナルでCtrl+Cを押下してください。コンテナが停止します。

## コンテナイメージのアップロード
(1) まだDocker Hubのアカウントを持っていない場合は、 https://hub.docker.com/ から作成してください。

(2) まだログインしていない場合は、次のコマンドでDocker Hubにログインしてください。 `xxxxxxxx` の部分は自分のDocker Hubユーザー名を入力してください。

```bash
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: xxxxxxxx
Password: 
Login Succeeded
```

(3) 次のコマンドで、コンテナイメージに自分のユーザー名を含むタグを付加してください。

```bash
$ docker tag spring-boot-sample:0.0.1 xxxxxxxx/spring-boot-sample:0.0.1
```

(4) 次のコマンドで、先ほど付加したタグを確認してください。コンテナイメージが2つあるように見えますが、1つのイメージに2つのタグが付加されているだけです（ `IMAGE ID` が同じであることから、同一のイメージであることが分かります）。

```bash
$ docker image ls
REPOSITORY                                                                    TAG                                                     IMAGE ID       CREATED         SIZE
spring-boot-sample                                                            0.0.1                                                   616af77ebd5e   6 minutes ago   655MB
mtada/spring-boot-sample                                                      0.0.1                                                   616af77ebd5e   6 minutes ago   655MB
```

(5) 次のコマンドで、コンテナイメージをDocker Hubにアップロードしてください。

```bash
$ docker image push xxxxxxxx/spring-boot-sample:0.0.1        
The push refers to repository [docker.io/mtada/spring-boot-sample]
708da93caf6c: Pushed 
9c1a40efc0ba: Pushed 
173ef8ff1904: Mounted from library/maven 
842787e3caf8: Mounted from library/maven 
360c474fed9f: Mounted from library/maven 
ac42267582ac: Mounted from library/maven 
5afe6e19ec87: Mounted from library/maven 
72f9283464d3: Mounted from library/maven 
d1ba9e4bcac4: Mounted from library/maven 
0.0.1: digest: sha256:5ae19cbcd70dce6e9b3339858e6b2dfb67daa2d8a9bf6e584ae7ae2b32bbf87e size: 2214
```

(6) ブラウザで https://hub.docker.com/ にアクセスしてください。コンテナイメージ一覧の中にspring-boot-sampleが確認できるはずです。
