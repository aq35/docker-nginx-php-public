cd contents
composer create-project --prefer-dist "laravel/laravel=8.*" laravel
php artisan -v

docker-compose exec db bash
mysql -u user -p database
> password