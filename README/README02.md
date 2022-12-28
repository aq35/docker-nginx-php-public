php.ini<br>
zend.exception_ignore_args<br>
開発 は off、本番 は on<br>
有効にすると例外のスタックトレースに引数情報が出なくなる<br>

expose_php<br>
開発 は on、本番 は off<br>
有効にするとHTTPヘッダに X-Powered-By: PHP/7.4.5 とPHPのバージョン情報が表示されます。<br>

max_execution_time<br>
設定値: 30(秒) デフォルト: 30(秒)<br>
1リクエストあたりの最大実行時間（秒）<br>
コマンドラインから実行する場合のデフォルト設定は 0 です。<br>
サーバーの負荷を上げることを防止するのに役立ちます。<br>

max_input_vars<br>
設定値: 1000(個) デフォルト: 1000(個)<br>
1リクエストで受け付ける最大の入力変数の数<br>
$_GET, $_POST, $_COOKIE それぞれ個別に適用されます。<br>
設定値を超える場合は E_WARNING が発生し、以降の入力変数はリクエストから削除されます。<br>

upload_max_filesize<br>
設定値: 20M デフォルト: 2M<br>
設定する単位に短縮表記を使えます<br>
アップロードされるファイルの最大サイズ。<br>

post_max_size<br>
設定値: 128M デフォルト: 8M<br>
upload_max_filesize の設定値より大きくする必要がある。<br>
POSTデータに許可される最大サイズを設定します。<br>
ファイルアップロードにも影響します。<br>

memory_limit<br>
設定値: 256M デフォルト: 128M<br>
post_max_size の設定値より大きくする必要がある。<br>
memory_limit > post_max_size > upload_max_filesize<br>
1リクエストあたりの最大メモリ使用量<br>

メモリ設定はサーバーやプロジェクトによるかと思います。<br>
最初から大量に確保するのではなく、必要に応じて上げていく<br>

error_reporting<br>
開発 は E_ALL、本番 は E_ALL & ~E_DEPRECATED & ~E_STRICT<br>
E_ALL は 全ての PHP エラーを表示する<br>
E_ALL & ~E_DEPRECATED & ~E_STRICT は 非推奨の警告エラーを除く PHP エラーを表示する。<br>
E_DEPRECATED は コードの相互運用性や互換性を維持するために PHP がコードの変更を提案する。<br>
E_STRICT は 実行時の注意、将来のバージョンで動作しなくなるコードについて警告する。<br>

display_errors<br>
開発 は on、本番 は off<br>
エラーをHTML出力の一部として画面に出力するかどうかを定義します。<br>
セキュリティ上、本番では off 推奨<br>

display_startup_errors<br>
開発 は on、本番 は off<br>
display_errors を on にした場合でも、PHPの起動シーケンスにおいて発生したエラーは表示されません。<br>
セキュリティ上、本番では off 推奨<br>

error_log<br>
スクリプトエラーが記録されるファイル名です。<br>

default_charset = UTF-8<br>
設定値: UTF-8 デフォルト: UTF-8<br>
デフォルト文字コード設定<br>
PHP 5.6.0 以降は "UTF-8" がデフォルトになりますが、念のため明示的に指定します。

date.timezone<br>
設定値: Asia/Tokyo デフォルト: GMT<br>
全ての日付/時刻関数で使用されるデフォルトのタイムゾーン。

mysqlnd.collect_memory_statistics<br>
開発 は on、本番 は off<br>
さまざまなメモリ統計情報の収集を有効にします。<br>
phpinfo() で mysqli の統計情報を出力するかどうか

zend.assertions<br>
開発 は 1、本番 は -1<br>
アサーションのコードを生成して実行します<br>
1 アサーションのコードを生成して実行します (開発モード)<br>
0 アサーションのコードは生成しますが実行時にはスキップします (実行しません)<br>
-1 アサーションのコードを生成せず、アサーションのコストがゼロになります (実運用モード)<br>

mbstring.language<br>
設定値: Japanese デフォルト: neutral<br>
mbstring で使用される言語設定のデフォルト値。<br>

opcache の設定<br>
本番のみ有効にします。<br>
opcacheするとソースコードのキャッシュ、最適化して高速化が見込めます。<br>
ソースコードを変更してもサーバーを再起動しないと変更が反映されなくなるため開発時は使用しません。<br>

opcache.enable<br>
オペコード・キャッシュを有効にします。<br>

opcache.interned_strings_buffer<br>
インターン (intern) された文字列を格納するために使用されるメモリ量。（MB単位）<br>

opcache.max_accelerated_files<br>
OPcache ハッシュテーブルのキー（すなわちスクリプト）の最大数<br>

opcache.validate_timestamps<br>
有効にすると、OPcache は、スクリプトが更新されたか opcache.revalidate_freq 秒ごとにチェックします。<br>
無効にすると、スクリプトの更新をチェックしません。<br>

opcache.huge_code_pages<br>
PHPコード(textセグメント)を HUGE PAGE にコピーする機能を有効にしたり、無効にしたりできます。<br>
これにより、パフォーマンスは向上するはずですが、適切なOSの設定が必要です。<br>
※適切なOS設定がいまいちわからなかったので、この設定は無効化しています。<br>

opcache.preload<br>
サーバが起動した際にコンパイルされ、実行されるPHPスクリプトを指定します。<br>
PHP7.4以降の設定<br>
※ここはプロジェクトに合わせて自前で用意する必要があります。これは無理に設定しなくてもokと思います。<br>

opcache.preload_user<br>
root ユーザでコードをあらかじめロードすることは、セキュリティ上の理由から禁止されています。<br>
PHPの実行ユーザーを指定します。<br>
PHP7.4以降の設定