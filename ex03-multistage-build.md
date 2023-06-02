演習3 マルチステージビルド
====================

# この演習の目的
マルチステージビルドを利用して、Java（Spring Boot）アプリケーションのビルドおよびコンテナ化を行います。

本演習のコマンドは、すべてex03フォルダで実行してください。

```shell
$ cd ex03
```

> Spring Bootアプリケーションをコンテナ化する方法には、他にも[Buildpacks](https://spring.pleiades.io/spring-boot/docs/3.1.0/maven-plugin/reference/htmlsingle/#build-image)や[Jib](https://cloud.google.com/java/getting-started/jib?hl=ja)を利用する方法があります。
> これらを利用すると、Dockerfileを書くことなくコンテナ化ができます。

# 手順
## マルチステージビルド
(1) エディタなどでDockerfileの内容を確認してください。

```Dockerfile
# for build
FROM maven:3.9.2-eclipse-temurin-17-alpine AS build
COPY . /home/maven
WORKDIR /home/maven
RUN mvn package

# for packaging container image
FROM bellsoft/liberica-openjdk-alpine:17
COPY --from=build /home/maven/target/*.jar /srv/application.jar
RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot
USER springboot
EXPOSE 8080
CMD ["java", "-jar", "/srv/application.jar"]
```

(2) 次のコマンドで、コンテナイメージをビルドしてください（build.shに同じコマンドが書いてあります）。

> コンテナイメージの他に、各ライブラリのJARファイルもダウンロードされますので、時間がかかります。

```shell
$ docker image build -t spring-boot-sample:0.0.1 .
[+] Building 4.0s (15/15) FINISHED                                                                                                                              
 => [internal] load build definition from Dockerfile                                                                                                       0.0s
 => => transferring dockerfile: 69B                                                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                          0.0s
 => => transferring context: 2B                                                                                                                            0.0s
 => [internal] load metadata for docker.io/bellsoft/liberica-openjdk-debian:17                                                                             3.7s
 => [internal] load metadata for docker.io/library/maven:3.9.2-eclipse-temurin-17-alpine                                                                   3.7s
 => [auth] bellsoft/liberica-openjdk-debian:pull token for registry-1.docker.io                                                                            0.0s
 => [auth] library/maven:pull token for registry-1.docker.io                                                                                               0.0s
 => [internal] load build context                                                                                                                          0.0s
 => => transferring context: 1.89kB                                                                                                                        0.0s
 => [stage-1 1/3] FROM docker.io/bellsoft/liberica-openjdk-debian:17@sha256:74a24c81389c0cb40ffd7dd68493248835c22171dd33dfb91f921b754d23cbab               0.0s
 => [build 1/4] FROM docker.io/library/maven:3.9.2-eclipse-temurin-17-alpine@sha256:df95dbe4c6d2f8474778342c0c2c6936a638154fab50cc9e6e3dcd6a0446e46a       0.0s
 => CACHED [build 2/4] COPY . /home/maven                                                                                                                  0.0s
 => CACHED [build 3/4] WORKDIR /home/maven                                                                                                                 0.0s
 => CACHED [build 4/4] RUN mvn package                                                                                                                     0.0s
 => CACHED [stage-1 2/3] COPY --from=build /home/maven/target/*.jar /srv/application.jar                                                                   0.0s
 => CACHED [stage-1 3/3] RUN groupadd -r springboot && useradd --no-log-init -r -g springboot springboot                                                   0.0s
 => exporting to image                                                                                                                                     0.0s
 => => exporting layers                                                                                                                                    0.0s
 => => writing image sha256:d9aafb45fb44d62933a21bca7f89c76dc95203606b1cb6bb169c896cddc4e677                                                               0.0s
 => => naming to docker.io/library/spring-boot-sample:0.0.1                                                                                                0.0s 
```

(3) 次のコマンドで、コンテナイメージが作成されたことを確認してください。

```shell
$ docker image ls
REPOSITORY                                        TAG       IMAGE ID       CREATED          SIZE
spring-boot-sample                                0.0.1     d9aafb45fb44   5 minutes ago    297MB
```

## コンテナの起動
(1) 次のコマンドで、コンテナを起動してください（run.shに同じコマンドが書いてあります）。

```shell
$ docker container run --rm -p 8080:8080 spring-boot-sample:0.0.1

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.1.0)

2023-06-02T01:02:10.806Z  INFO 1 --- [           main] c.e.s.SpringBootSampleApplication        : Starting SpringBootSampleApplication v0.0.1-SNAPSHOT using Java 17.0.7 with PID 1 (/srv/application.jar started by springboot in /)
2023-06-02T01:02:10.829Z  INFO 1 --- [           main] c.e.s.SpringBootSampleApplication        : No active profile set, falling back to 1 default profile: "default"
2023-06-02T01:02:13.712Z  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2023-06-02T01:02:13.737Z  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2023-06-02T01:02:13.740Z  INFO 1 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.8]
2023-06-02T01:02:13.974Z  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2023-06-02T01:02:13.980Z  INFO 1 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 2919 ms
2023-06-02T01:02:14.573Z  INFO 1 --- [           main] o.s.b.a.w.s.WelcomePageHandlerMapping    : Adding welcome page template: index
2023-06-02T01:02:15.113Z  INFO 1 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2023-06-02T01:02:15.163Z  INFO 1 --- [           main] c.e.s.SpringBootSampleApplication        : Started SpringBootSampleApplication in 6.602 seconds (process running for 8.169)
```

(2) ブラウザで http://localhost:8080 にアクセスしてください。「Spring Boot Sample」と表示されれば成功です。

(3) ターミナルでCtrl+Cを押下してください。コンテナが停止します。

## コンテナイメージのアップロード
(1) まだDocker Hubのアカウントを持っていない場合は、[アカウントとアクセストークンを作成](ex02-building-image.md#コンテナイメージのアップロード)してください。

(2) まだログインしていない場合は、次のコマンドで、Docker Hubにログインしてください。 `xxxxxxxx` の部分は自分のDocker Hubユーザー名を入力してください。Passwordには、パスワードではなくアクセストークンを入力してください。

```shell
$ docker login -u xxxxxxxx
Password: 
Login Succeeded
```

(3) 次のコマンドで、コンテナイメージに自分のユーザー名を含むタグを付加してください。 `xxxxxxxx` の部分は自分のDocker Hubユーザー名を入力してください。

```shell
$ docker tag spring-boot-sample:0.0.1 xxxxxxxx/spring-boot-sample:0.0.1
```

(4) 次のコマンドで、先ほど付加したタグを確認してください。コンテナイメージが2つあるように見えますが、1つのイメージに2つのタグが付加されているだけです（ `IMAGE ID` が同じであることから、同一のイメージであることが分かります）。

```shell
$ docker image ls
REPOSITORY                                        TAG       IMAGE ID       CREATED          SIZE
spring-boot-sample                                0.0.1     d9aafb45fb44   6 minutes ago    297MB
mtadacs/spring-boot-sample                        0.0.1     d9aafb45fb44   6 minutes ago    297MB
```

(5) 次のコマンドで、コンテナイメージをDocker Hubにアップロードしてください。 `xxxxxxxx` の部分は自分のDocker Hubユーザー名を入力してください。

```shell
$ docker image push xxxxxxxx/spring-boot-sample:0.0.1
The push refers to repository [docker.io/mtadacs/spring-boot-sample]
1802536ee673: Pushed 
3825d9766f3a: Pushed 
de45842e4853: Mounted from bellsoft/liberica-openjdk-debian 
4773bcfce180: Mounted from bellsoft/liberica-openjdk-debian 
ae56c0c5405b: Mounted from bellsoft/liberica-openjdk-debian 
0.0.1: digest: sha256:bda06106956d4223540d17fa684e5b1e5283e568c23a6bc26b2ac7933137310e size: 1374
```

(6) ブラウザで https://hub.docker.com/ にアクセスしてください。コンテナイメージ一覧の中にspring-boot-sampleが確認できるはずです。
