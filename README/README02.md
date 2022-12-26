## 02 Ubuntuを立ててみる。

04.徒然なるままに
Laravel SailのDockerfileの中身を調べてみる。

```
FROM ubuntu:22.04

LABEL maintainer="適当"

ARG Dockerの変数

WORKDIR /var/www/html コンテナ内の作業場所

ENV DEBIAN_FRONTEND noninteractive 入力待ちをブロックしなくなる変数
ENV TZ=Asia/Tokyo タイムゾーンらしき変数 

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
タイムゾーンの設定

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

### Linuxの権限
RUN groupadd --force -g $WWWGROUP sail
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail

### ここら辺、laravel環境をセットアップするっぽい
COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /etc/php/8.2/cli/conf.d/99-sail.ini
RUN chmod +x /usr/local/bin/start-container

EXPOSE 8000

ENTRYPOINT ["start-container"]
```

```
version: '3'
services:
    laravel.test:
        build:
            context: ./ubuntu
            dockerfile: Dockerfile
            args:
                WWWGROUP: '${WWWGROUP}'
        image: sail-8.1/app
        extra_hosts:
            - 'host.docker.internal:host-gateway'
        ports:
            - '80:80'
            - '5173:5173'
        environment:
            WWWUSER: 'sail'
            LARAVEL_SAIL: 1
            XDEBUG_MODE: '-off'
            XDEBUG_CONFIG: '-client_host=host.docker.internal'
        volumes:
            - '.:/var/www/html'
        networks:
            - sail
networks:
    sail:
        driver: bridge
```

$ docker-compose up -d

以下で失敗した。
RUN groupadd --force -g $WWWGROUP sail
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 sail
グループを追加、ユーザを追加

./vendor/laravel/sail/src/Console/InstallCommand.php
$environment .= "\nWWWGROUP=1000";
$environment .= "\nWWWUSER=1000\n";

groupadd --force -g <Group-ID> sail
Sailの権限戦略は、sailコンテナでは、数字のIDでローカルユーザーをグループ、ユーザーにマッピングするようだ

再度、以下でコンテナ作成
$ docker-compose up -d
localhost:80をブラウザを叩いても、表示されないのは...WEBサーバーが起動してないから.
「nginx」を入れてみようと思う。
```
RUN apt-get update \
    && apt-get install -y -q nginx

最後に"daemon off;"をする。公式に書いてあった。
https://docs.docker.jp/engine/reference/run.html#d
CMD ["nginx", "-g", "daemon off;"]
```

メモ:Dockerfileを変更しても反映されないことがある。
laravelとかだと、定期的にキャッシュクリアしないといけない。
ので「キャッシュ」が関係しているかもしれない。
imageを削除したら、追加したコマンドも実行された

$ docker-compose up -d

localhost:80をブラウザを叩いたら、index.htmlが返ってきた。
達成感+1; Docker環境でWEBサーバーを立ち上げることができた。ちょっと用語がわかった。
自信の喪失-1; わからない用語が増えた。
