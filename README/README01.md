## コマンド整理

## 全て消す
docker-compose down --rmi all --volumes --remove-orphans

## ビルド
docker-compose build

## 起動
docker-compose up

##　コンテナに入る
docker-compose exec nginx bash
docker-compose exec php bash


## nginxの動作チェック
docker-compose exec php bash
php -v
composer -v
php -m インストール済の拡張機能の一覧
cat /usr/local/etc/php/php.ini php.iniがコピー出来ているか確認


##　php コンテナに入る
docker-compose exec php bash
cd laravel
php artisan migrate
php artisan db:seed

##　NPMビルドはローカルでやる
ビルド結果をプッシュする。