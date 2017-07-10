# Configuration

資料庫存取設定於 *config/database.php*。

可以定義多個 connection，接著決定預設使用哪個。

## 資料庫連結

可以建立新的具名連結，也可以在這些新的連結中設定驅動程式(MySQL、Postgres..etc)。

## 其他的資料庫設定選項

*config.database* 組態設定區段有很多其他的組態設定。可以設定 Redis 存取、自訂 migration 用的資料表名稱、決定預設連結，切換非 Eloquent 呼叫式要回傳 stdClass or array。

當使用任何一種允許多 connections 的 laravel 服務時(包括受到資料庫或檔案儲存支持庫的 session、可使用 Redis or Memcached 的快取、可使用 MySQL or PostgreSQL 的料庫)，可以定義多重連結，也可以選擇特定的連結是預設的。

特定連結方式:

```php
$users = DB::connection('secondary')->select('select * from users');
```