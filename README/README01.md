## 01 Docker Compose を学ぶ

01.徒然なるままに<br>
[「Laravel Sail」](https://laravel.com/docs/9.x/sail)で、Docker環境を初めて触っててからいくらか時間が経った。<br>
Dockerとは、開発環境を作ってくれるものだ。MAMPとかと違って、Docker関係の設定を皆が共有できるため、エンジニアのローカル環境の違いによる問題を減らしてくれる。<br>
PHPのバージョンが微妙に違って、実行結果が微妙に異なるトラブルとか。<br>

そろそろ、自力で開発環境を構築してみてもいいかもしれない。

まずは、以下公式ページをざっとみてみる。
## Docker Compose を試す
https://docs.docker.com/compose/gettingstarted/


1.Compose ファイルでサービスを定義する

docker-compose.yml
```xml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
```

2.Compose でアプリをビルドして実行する<br>
$ docker compose up<br>

webと、redisの二つのサービスが立ち上がる<br>

02.徒然なるままに<br>
docker-composeで複数のサービスを定義してビルドできることがわかった。<br>
分からないことが多すぎるが、深く考えないことにする。<br>

次はどこへ。偉大なるlaravel/sailへ<br>

## Laravel/Sail
https://github.com/laravel/sail

MariaDB
```xml
    mariadb:
        image: 'mariadb:10'
        ports:
            - '${FORWARD_DB_PORT:-3306}:3306'
        environment:
            MYSQL_ROOT_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ROOT_HOST: "%"
            MYSQL_DATABASE: '${DB_DATABASE}'
            MYSQL_USER: '${DB_USERNAME}'
            MYSQL_PASSWORD: '${DB_PASSWORD}'
            MYSQL_ALLOW_EMPTY_PASSWORD: 'yes'
        volumes:
            - 'sail-mariadb:/var/lib/mysql'
            - './vendor/laravel/sail/database/mysql/create-testing-database.sh:/docker-entrypoint-initdb.d/10-create-testing-database.sh'
        networks:
            - sail
        healthcheck:
            test: ["CMD", "mysqladmin", "ping", "-p${DB_PASSWORD}"]
            retries: 3
            timeout: 5s
```

ports:<br>
FORWARD_DB_PORT ホストにHTTPリクエストしたら、コンテナにHTTPリクエストが渡る。<br>
- '${FORWARD_DB_PORT:-3306}:3306'
  
volumes:<br>
ホスト・コンテナ間でのファイル共有ができる。<br>
ホスト側のパス:コンテナ側のパス。ホスト側でファイル操作した時、コンテナのソースが同期されてたのは、volumesのおかげ。

```
    mailhog:
        image: 'mailhog/mailhog:latest'
        ports:
            - '${FORWARD_MAILHOG_PORT:-1025}:1025'
            - '${FORWARD_MAILHOG_DASHBOARD_PORT:-8025}:8025'
        networks:
            - sail
```
1025:MAILHOGが実際にメールを待ち受けている<br>
8025:WEBサービスでMAILHOGの管理画面<br>

```    
# For more information: https://laravel.com/docs/sail
version: '3'
services:
    laravel.test:
        build:
            context: ./vendor/laravel/sail/runtimes/8.1
            dockerfile: Dockerfile
            args:
                WWWGROUP: '${WWWGROUP}'
        image: sail-8.1/app
        extra_hosts:
            - 'host.docker.internal:host-gateway'
        ports:
            - '${APP_PORT:-80}:80'
            - '${VITE_PORT:-5173}:${VITE_PORT:-5173}'
        environment:
            WWWUSER: '${WWWUSER}'
            LARAVEL_SAIL: 1
            XDEBUG_MODE: '${SAIL_XDEBUG_MODE:-off}'
            XDEBUG_CONFIG: '${SAIL_XDEBUG_CONFIG:-client_host=host.docker.internal}'
        volumes:
            - '.:/var/www/html'
        networks:
            - sail
{{depends}}
{{services}}
networks:
    sail:
        driver: bridge
{{volumes}}
```

- '${APP_PORT:-80}:80'　WEBサービス
- '${VITE_PORT:-5173}:${VITE_PORT:-5173}' VITEの開発環境のHMRっていう仕組みに利用

コンテナ間で通信ができるようになる。
```
networks:
    sail:
        driver: bridge
```
それぞれのコンテナに「- sail」があったのはそういうことか。


```
FROM ubuntu:22.04

LABEL maintainer="Taylor Otwell"

ARG WWWGROUP
ARG NODE_VERSION=18
ARG POSTGRES_VERSION=14

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
    && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 dnsutils \
    && curl -sS 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x14aa40ec0831756756d7f66c4f4ea0aae5267a6c' | gpg --dearmor | tee /usr/share/keyrings/ppa_ondrej_php.gpg > /dev/null \
    && echo "deb [signed-by=/usr/share/keyrings/ppa_ondrej_php.gpg] https://ppa.launchpadcontent.net/ondrej/php/ubuntu jammy main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
    && apt-get update \
    && apt-get install -y php8.2-cli php8.2-dev \
       php8.2-pgsql php8.2-sqlite3 php8.2-gd \
       php8.2-curl \
       php8.2-imap php8.2-mysql php8.2-mbstring \
       php8.2-xml php8.2-zip php8.2-bcmath php8.2-soap \
       php8.2-intl php8.2-readline \
       php8.2-ldap \
       php8.2-msgpack php8.2-igbinary php8.2-redis php8.2-swoole \
       php8.2-memcached php8.2-pcov php8.2-xdebug \
    && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
    && curl -sLS https://deb.nodesource.com/setup_$NODE_VERSION.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm \
    && curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | gpg --dearmor | tee /usr/share/keyrings/yarn.gpg >/dev/null \
    && echo "deb [signed-by=/usr/share/keyrings/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list \
    && curl -sS https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | tee /usr/share/keyrings/pgdg.gpg >/dev/null \
    && echo "deb [signed-by=/usr/share/keyrings/pgdg.gpg] http://apt.postgresql.org/pub/repos/apt jammy-pgdg main" > /etc/apt/sources.list.d/pgdg.list \
    && apt-get update \
    && apt-get install -y yarn \
    && apt-get install -y mysql-client \
    && apt-get install -y postgresql-client-$POSTGRES_VERSION \
    && apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.2

RUN groupadd --force -g $WWWGROUP sail
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail

COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /etc/php/8.2/cli/conf.d/99-sail.ini
RUN chmod +x /usr/local/bin/start-container

EXPOSE 8000

ENTRYPOINT ["start-container"]
```

03.徒然なるままに<br>
phpの拡張機能とか、composer、npm辺りは、local開発環境でセットアップする際に馴染みがある<br>
FROM,LABEL,ARG,ENV,RUN,COPY,EXPOSE,ENTRYPOINT ここら辺とかパターンな気がする。<br>
