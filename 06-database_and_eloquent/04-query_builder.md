# Query Builder

流暢介面是主要使用方法鏈結來提供簡單的 API，以供用戶使用的介面。

```php
// 非流暢
$users = DB::select(['table' => 'users', 'where' => ['type' => 'donor']]);

// 流暢
$users = DB::table('users')->where('type', 'donor')->get();
```

## DB 靜態介面的基本用法

DB 靜態介面可建立查詢產生器鏈結以及簡單的原生查詢

```php
// 基本陳述式
DB::statement('drop table users');

// 原生的 select 以及參數綁定
DB::select('select * from contacts where validated = ?', [true]);

// 使用流暢的產生器來選擇
$user = DB::table('users')->get();

// 鏈結及其他複雜的呼叫式
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
            ->where('contacts.type', 'donor');
    })
    ->get();
```

## 原生 SQL

常見的動作 `select()`, `insert()`, `update()`, `delete()`，`update()`, `delete()` 會還船被影響的列數，`statement()` 不會。

### 原生的選擇

最簡單的 DB 方法是 `select()`

```php
$users = DB::select('select * from users');
```

### 參數綁定與具名綁定

Laravel 的資料庫結構可使用 PDO 資料綁定，可避免 SQL 攻擊:

```php
$usersOfType = DB::select('select * from users where type = ?', [$type]);
```

也可以明確地為這些參數命名:

```php
$usersOfType = DB::select('select * from users where type = :type', ['type' => $type]);
```
### 原生插入

```php
DB::insert('insert into contacts (name, email) values (?, ?)', ['sally', 'sally@me.com']);
```

### 原生更新

```php
$countUpdated = DB::update('update contacts set status = ? where id = ?', ['donor', $id]);
```

### 原生刪除

```php
$countDeleted = DB::delete('delete from contacts where archived = ?', [true]);
```

## 使用查詢產生器來鏈結

```php
$usersOfType = DB::table('users')
    ->where('type', $type)
    ->get();
```

### 限制方法

#### select()