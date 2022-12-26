## 03 Nginxにphp-fpmを実行させる。


nginx では、URL が基本となっています。
つまり、HTTP要求リクエスト のURLに対して、どのように処理を行うか、ということが基本的な思想です。

nginx はURLに対する処理を設定ファイルで記述 するという方向性を持っています。

設定ファイルは、ディレクティブと言われる設定項目の集合

ディレクティブは適用範囲などを明確にするために、ブロック に分けることが出来る

HTTPブロック > serverブロック > locationブロック

書き方1
```
http {
    server {
        listen 80; // ディレクティブの listen はサーバがどのポートで待ち受けるかの指定
        server_name test.matsue-ct.ac.jp;//  server_name は文字通りサーバの名前を意味=バーチャルホスト名
        index index.html index.htm;
        location  /  {
            try_files $uri $uri/;
        }
        location /admin/ {
            index index.php;
        }
    }
}
```

書き方2
```
server {
    server_name hoge;
    root /home/pub;// Apache の DocumentRoot に相当
    location ~ \.php$ {　// 内部の location は正規表現で指定しており、 拡張子が .php が指定されているURLにマッチします
        index index.php;
    }
}
```

書き方3
```
    location ~ \.php$ {
        root           /var/www/html;　// Apache の DocumentRoot に相当効できるようになる。
        # fastcgi_pass   127.0.0.1:9000; 　# これは php-fpm の設定に依存する。
        fastcgi_pass    unix:/run/php-fpm/www.sock; #パスの設定は、php-fpm 側の記述に unix: を付けたものになる。
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root/$fastcgi_script_name;
        include        fastcgi_params;
    }

```

書き方4
Nginx が、.php ファイルをサービスすることを認識させるため、 デフォルトの location ブロックを修正します:
```
location / {
    root   /var/www/html;
    index  index.php index.html index.htm;
}
```

書き方5
.php ファイルへのリクエストが PHP-FPM バックエンドに渡されるようにすること
```
location ~* \.php$ {
    fastcgi_index   index.php;
    fastcgi_pass    127.0.0.1:9000;
    include         fastcgi_params;
    fastcgi_param   SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param   SCRIPT_NAME        $fastcgi_script_name;
}
```

Dockerfileに以下を追記
COPY ./infra/docker/nginx/*.conf /etc/nginx/conf.d/
ホスト側のnginx設定ファイルをコンテナ側にコピペする。

nginxは、PHPをうごかすためには、cgiがプログラムを動かすように設定する

PHP-FPMというのは、PHP-FastCGI Process Managerの略

PHP-FPM(=FastCGIのインターフェース) → このプログラムを使って、PHPを動作をさせます

nginx→php-fpmの通信はUnixドメインソケット通信です。

# unix socket
RUN mkdir /var/run/php-fpm
VOLUME ["/var/run/php-fpm"]


502 Bad Gateway
PHP-FPM is not running
NGINX can’t communicate with PHP-FPM
PHP-FPM is timing out

コンテナに入る
docker-compose exec laravel.test bash ...OK
phpを実行する
php index.php ...OK
php-fpmの設定ファイルが入っているか調べる
/etc/php/8.2/cli
/etc/php/8.2/fpm
ディレクトリは、8.2だ。

サービス起動時に、php8.2-fpm.serviceが起動されるか
systemctl list-unit-files --type=service | grep php
php8.2-fpm.service                     enabled  enabled
phpsessionclean.service                static   -

directory index of "/var/www/html/" is forbidden