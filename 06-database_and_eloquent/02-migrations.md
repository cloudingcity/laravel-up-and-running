# Migrations

Laravel 可輕鬆使用程式碼驅動的 migration 來定義資料庫結構。每一個資料表、欄位、索引、key都可以用程式碼定義。

## 定義 migration

migration 是一個檔案，定義了 up down，執行你想做的修改。

migration 一定會按照日情順序來執行，從最早的開始，接著執行 up()，要恢復則是 down()。

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateTasksTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('tasks', function (Blueprint $table) {
            $table->increments('id');
            $table->string('title');
            $table->text('body');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('tasks');
    }
}
```

## 建立 migration

```
php artisan make:migration create_users_table
```

- `--create=table_name`: 會對 migration 預先填入建立名為 table_name 的資料表程式碼。
- `--table=table_name`: 會對 migration 預先填入修改現有資料表的程式碼。

## 建立資料表

要在 migration 中建立新的資料表，可以用 `create()`:

- 第一個參數是資料表名稱
- 第二個是定義欄位的 closure

```php
Schema::create('tablename', function (Blueprint $table) {
    // columns
});
```

## 建立欄位

要在資料表中建立新欄位，無論使用 create() or table()，都可以將 Blueprint 的實例傳入 closure。

Blueprint 欄位方法:

- INTEGER 型態欄位
    - integer(colName)
    - tinyInteger(colName)
    - smallInteger(colName)
    - mediumInteger(colName)
    - bigInteger(colName)
- VARCHAR 型態欄位
    - string(colName, OPTIONAL length)
- BLOB 型態欄位
    - binary(colName)
- BOOLEAN 型態欄位 (MySQL: 為一個 TINYINT(1))
    - boolean(colName)
- CHAR 型態欄位
    - char(colNamem Length)
- DATATIME 型態欄位
    - datetime(colName)
- DECIMAL 型態欄位，使用位數與小數位數，e.q. decimal('amount', 5, 2) = 位數5 小數2
    - decimal(colName, precision, scale)
- DOUBLE 型態欄位，e.q. double('tolerance', 12, 8) =  7204.05671739
    - double(colName, total digits, digits after decimal)
- ENUM 型態欄位，使用所提供的選項
    - enum(colName, [choiceOne, choiceTwo])
- FLOAT 型態欄位 (MySQL: double)
    - float(colName)
- JSON 或 JSONB 型態欄位
    - json(colName)
    - jsonb(colName)
- TEXT 型態欄位
    - text(colName)
    - mediumText(colName)
    - longText(colName)
- TIME 型態欄位
    - time(colName)
- TIMESTAMP 型態欄位
    - timestamp(colName)
- UUID 型態欄位 (MySQL: CHAR(36))
    - uuid(colName)

特殊 Blueprint 方法:

- 添加一個不帶符號遞增的 INTEGER or BIG INTEGER 主鍵 ID
    - increments(colName)
    - bigIncrements(colName)
- 添加 created_at 與 updated_at 時間戳欄位
    - timestamps()
    - nullableTimestamps(colName)
- 位用戶 "記住我" token 添加 remember_token 欄位 (VARCHAR(100))
    - rememberToken()
- 添加一個 deleted_at 時間戳提供虛刪除 (soft delete) 使用
    - softDeletes()
- 為所提供的 +colName+ 添加一個整數 colName_id 與 一個字串 colName_type (例如 morphs('tag') 可添加整數 tag_id 與字串 tag_type); 再多行關西中使用
    - morphs(colName)

## 流暢地建立額外的特性


## 卸除資料表

```
Schema::drop('contacts');
```

## 修改欄位

## 索引與外建

## 執行 migration