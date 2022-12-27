zend.exception_ignore_args
開発 は off、本番 は on
有効にすると例外のスタックトレースに引数情報が出なくなる

expose_php
開発 は on、本番 は off
有効にするとHTTPヘッダに X-Powered-By: PHP/7.4.5 とPHPのバージョン情報が表示されます。

max_execution_time
設定値: 30(秒) デフォルト: 30(秒)
1リクエストあたりの最大実行時間（秒）
コマンドラインから実行する場合のデフォルト設定は 0 です。
サーバーの負荷を上げることを防止するのに役立ちます。

max_input_vars
設定値: 1000(個) デフォルト: 1000(個)
1リクエストで受け付ける最大の入力変数の数
$_GET, $_POST, $_COOKIE それぞれ個別に適用されます。
設定値を超える場合は E_WARNING が発生し、以降の入力変数はリクエストから削除されます。

upload_max_filesize
設定値: 20M デフォルト: 2M
設定する単位に短縮表記を使えます
アップロードされるファイルの最大サイズ。

post_max_size
設定値: 128M デフォルト: 8M
upload_max_filesize の設定値より大きくする必要がある。
POSTデータに許可される最大サイズを設定します。
ファイルアップロードにも影響します。

memory_limit
設定値: 256M デフォルト: 128M
post_max_size の設定値より大きくする必要がある。
memory_limit > post_max_size > upload_max_filesize
1リクエストあたりの最大メモリ使用量

メモリ設定はサーバーやプロジェクトによるかと思います。
最初から大量に確保するのではなく、必要に応じて上げていく

error_reporting
開発 は E_ALL、本番 は E_ALL & ~E_DEPRECATED & ~E_STRICT
E_ALL は 全ての PHP エラーを表示する
E_ALL & ~E_DEPRECATED & ~E_STRICT は 非推奨の警告エラーを除く PHP エラーを表示する。
E_DEPRECATED は コードの相互運用性や互換性を維持するために PHP がコードの変更を提案する。
E_STRICT は 実行時の注意、将来のバージョンで動作しなくなるコードについて警告する。

display_errors
開発 は on、本番 は off
エラーをHTML出力の一部として画面に出力するかどうかを定義します。
セキュリティ上、本番では off 推奨

display_startup_errors
開発 は on、本番 は off
display_errors を on にした場合でも、PHPの起動シーケンスにおいて発生したエラーは表示されません。
セキュリティ上、本番では off 推奨

error_log
スクリプトエラーが記録されるファイル名です。

default_charset = UTF-8
設定値: UTF-8 デフォルト: UTF-8
デフォルト文字コード設定
PHP 5.6.0 以降は "UTF-8" がデフォルトになりますが、念のため明示的に指定します。

date.timezone
設定値: Asia/Tokyo デフォルト: GMT
全ての日付/時刻関数で使用されるデフォルトのタイムゾーン。

mysqlnd.collect_memory_statistics
開発 は on、本番 は off
さまざまなメモリ統計情報の収集を有効にします。
phpinfo() で mysqli の統計情報を出力するかどうか

zend.assertions
開発 は 1、本番 は -1
アサーションのコードを生成して実行します
1 アサーションのコードを生成して実行します (開発モード)
0 アサーションのコードは生成しますが実行時にはスキップします (実行しません)
-1 アサーションのコードを生成せず、アサーションのコストがゼロになります (実運用モード)

mbstring.language
設定値: Japanese デフォルト: neutral
mbstring で使用される言語設定のデフォルト値。

opcache の設定
本番のみ有効にします。
opcacheするとソースコードのキャッシュ、最適化して高速化が見込めます。
ソースコードを変更してもサーバーを再起動しないと変更が反映されなくなるため開発時は使用しません。

opcache.enable
オペコード・キャッシュを有効にします。

opcache.interned_strings_buffer
インターン (intern) された文字列を格納するために使用されるメモリ量。（MB単位）

opcache.max_accelerated_files
OPcache ハッシュテーブルのキー（すなわちスクリプト）の最大数

opcache.validate_timestamps
有効にすると、OPcache は、スクリプトが更新されたか opcache.revalidate_freq 秒ごとにチェックします。
無効にすると、スクリプトの更新をチェックしません。

opcache.huge_code_pages
PHPコード(textセグメント)を HUGE PAGE にコピーする機能を有効にしたり、無効にしたりできます。
これにより、パフォーマンスは向上するはずですが、適切なOSの設定が必要です。
※適切なOS設定がいまいちわからなかったので、この設定は無効化しています。

opcache.preload
サーバが起動した際にコンパイルされ、実行されるPHPスクリプトを指定します。
PHP7.4以降の設定
※ここはプロジェクトに合わせて自前で用意する必要があります。これは無理に設定しなくてもokと思います。

opcache.preload_user
root ユーザでコードをあらかじめロードすることは、セキュリティ上の理由から禁止されています。
PHPの実行ユーザーを指定します。
PHP7.4以降の設定