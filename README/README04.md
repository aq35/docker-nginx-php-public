cd contents<br>
composer create-project --prefer-dist "laravel/laravel=8.*" laravel<br>
php artisan -v<br>

docker-compose exec db bash<br>
mysql -u user -p database<br>
> password

contentsにlaravelを使って、User::findが叩けるように<br>
以下が出力されてたので、DB接続自体は、OKそう。<br>
SQLSTATE[42S02]: Base table or view not found: 1146 Table 'database.users'