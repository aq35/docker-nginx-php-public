# コマンド整理

## 全て消す
docker-compose down --rmi all --volumes --remove-orphans

## ビルド
docker-compose build

## 起動
docker-compose up

##　コンテナに入る
docker-compose exec nginx bash<br>
docker-compose exec php bash


## nginxの動作チェック
docker-compose exec php bash<br>
php -v<br>
composer -v<br>
php -m インストール済の拡張機能の一覧<br>
cat /usr/local/etc/php/php.ini php.iniがコピー出来ているか確認


##　php コンテナに入る
docker-compose exec php bash<br>
cd laravel<br>
php artisan migrate<br>
php artisan db:seed

##　NPMビルドはローカルでやる
ビルド結果をプッシュする。