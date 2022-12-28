## docker-compose メモ
depends_on:<br>
サービスの起動順序を制御します。<br>
nginx の depends_on に php と書いているので、 nginx → php の順に起動するように指定しています<br>

各サービスがコンテナ名の名前解決を必要とするタイミング<br>
php側はphp:9000でnginx側の接続をListenする。php側としては他サービスが勝手に接続してくるだけなので名前解決は必要ない。<br>
nginx側はdefault.confにて*.phpのリクエストをphp:9000に投げる設定をしている。<br>
nginx起動時に対象のサービス、つまりホスト名phpがネットワーク上に存在するかどうかDNSに確認している。<br>
DNSからサービス名の情報が得られなかった場合、起動失敗となりエラー終了する。<br>
つまり、nginx側はサービスがstartしてからシステムとして準備完了となるまでにサービス名の解決を必要としている。<br>

nginx側でdepends_onにphpを指定している場合、<br>
phpコンテナがstartしてからnginxコンテナがcreateもしくはstartするのが保証されているので、<br>
名前解決によるエラーは発生しないものと考えられる。<br>

しかしdepends_onを指定していない場合、<br>
双方のコンテナが同時にcreateもしくはstartする。<br>
コンテナのstartタイミングをそろえてくれているわけではなさそうなので状況によってはphp側がネットワークにサービス名を登録するまでにnginx側が先にstartしてしまうかもしれない。<br>

メモ:<br>
docker compose up<br>
1.ネットワークの作成<br>
2.build：dockerイメージの作成。<br>
3.create：コンテナの作成。<br>
4.start：作成したコンテナの起動。<br>
5.attach：起動したコンテナの標準入力、標準エラー出力をターミナルに接続<br>

1,2,3はすでに作成されている場合はスキップされる<br>
depend_onが指定されているとcreateとstartの順番がコントロールされる<br>

<br>
  db:    <br>
    volumes:<br>
      - mysql-volume:/var/lib/mysql<br>
volumes:<br>
  mysql-volume:<br>
Volumeとは、コンテナを破棄してもデータを永続的に保存できるように、コンテナ外に提供されているデータの保存領域です。<br>
Dockerの管理下でホスト上にストレージ領域を確保しており、Linux なら /var/lib/docker/volumes/以下にあります。<br>

my.cnf<br>
これは結構適当なので必要に応じて変更した方がよいかもです。ローカル用なのでスロークエリとか出力していますが、<br>
本番等では設定入れないようにしましょう！<br>

ln -s /contents/laravel/public ./app<br>