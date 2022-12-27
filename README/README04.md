cd contents
composer create-project --prefer-dist "laravel/laravel=8.*" laravel
php artisan -v

docker-compose exec db bash
mysql -u user -p database
> password

contentsにlaravelを使って、User::findが叩けるように
以下が出力されてたので、OKそう。
SQLSTATE[42S02]: Base table or view not found: 1146 Table 'database.users'