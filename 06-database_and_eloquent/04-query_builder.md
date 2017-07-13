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

這些方法會以原樣接收查詢指令，並將它的回傳限制為較小的資料集合；

#### select()

可讓你選擇欄位；

```php
$emails = DB::table('contacts')
    ->select('email', 'email2 as second_email')
    ->get();

// or

$emails = DB::table('contacts')
    ->select('email')
    ->addSelect('email2 as second_email');
    ->get();
```

#### where

可讓你使用 WHERE 來限制被回傳的東西範圍，預設情況 `where()` 會接收三個參數；
1. 欄位
2. 比較運算子
3. 值

```php
$newContacts = DB::table('contact')
    ->where('created_at', '>', Carbon::now()->subDay())
    ->get();
```

如果運算子是 `=`，就可以省略；

```php
$vipContacts = DB::table('contacts')->where('vip', true)->get();
```

如果想要結合 `where()`，可一一鏈結或傳入一個陣列組陳的陣列；

```php
$newVips = DB::table('contacts')
    ->where('vip', true)
    ->where('created_at', '>', Carbon::now()->subDay());

// or
$newVips = DB::table('contacts')->where([
    ['vip', true],
    ['created_at', '>', Carbon::now()->subDay()].
]);
```

#### orWhere()

```php
$priorityContacts = DB::table('contacts')
        ->where('vip', true)
        ->orWhere('created_at', '>', Carbon::now()->subDay())
        ->get();
```

要建立複雜多條件，可以傳一個 closure 給 `orWhere()`；

```php
$contacts = DB::table('contacts')
        ->where('vip', true)
        ->orWhere(function ($query) {
            $query->where('created_at', '>', Carbon::now()->subDay())
                ->where('trial', false);
        })
        ->get();
```

#### whereBetween(colName, [Low, high])

可讓你限制查詢範圍

```php
$mediumDrinks = DB::table('drinks')
    ->whereBetween('size', [6, 12])
    ->get();
```

`whereNotBetween()` 是相反。

#### whereIn(colName, [1, 2, 3])

```php
$closeBy = DB::table('contacts')
    ->whereIn('state', ['FL', 'GA', 'AL'])
    ->get();
```

`whereNotIn` 是相反。

#### whereNull(colName) whereNotNull(colName)

分別選擇指定欄位為 NULL 或 NOT NULL。

#### whereRaw()

讓你傳入一個原生的、未逸出的字串。

```php
$goofs = DB::table('contacts')
    ->whereRaw('id = 12345')
    ->get();
```

> 小心 SQL Injection 盡量不要使用這個

#### whereExists()

當對它傳入一個子查詢時，只選擇至少回傳一列資料的資料列。

如果想取得至少留下一個評論的用戶；

```php
$commenters = DB::table('users')
    ->whereExists(function ($query) {
        $query->select('id')
            ->from('comments')
            ->whereRaw('comments.user_id = users.id');
    });
    ->get();
```

#### distinct

選擇唯一資料列。常與 `select()` 一起使用。

```php
$lastNames = DB::table('contacts')
    ->select('last_name')
    ->disinct()
    ->get();
```

### 修改方法

這些方法會改變查詢結果的輸出方式，而非只限制它的結果；

#### orderBy(conName, direction)

排序結果。第二個參數 asc/desc；

```php
$contacts = DB::table('contacts')
    ->orderBy('last_name', 'asc')
    ->get();
```

#### groupBy(), having(), havingRaw()

欄位結果分組。having(), havingRaw() 是選用的，可讓你根據群組的屬性來篩選結果。

尋找至少有 30 人的城市；

```php
$populousCities = DB:;table('contacts')
    ->groupBy('city')
    ->havingRaw('count(contact_id) > 30')
    ->get();
```

#### skip(), take()

最常用在分頁，定義要回傳多少列，開始回傳前要跳過多少列。

分業系統內的頁數與頁面大小；

```php
$page4 = DB::table('contacts')
    ->skip(30)
    ->take(10)
    ->get();
```

#### latest(conName), oldest(colName)

被傳入的欄位以降冪(lastest()) or 昇冪(oldest()) 排序，如果沒有傳入欄位，以 `created_at` 來排序。

#### inRandomOrder()

隨機排序結果。

### 結束 / 回傳方法

這些方法會停止查詢鏈，並觸發 SQL 查詢的執行；

#### get()

取得查詢的所有結果。

#### first(), firstOrFail()

只取得第一個結果，很像 `get()`，但加入 LIMIT 1；

```php
$newestContact = DB:: table('contacts')
    ->orderBt('created_at')
    ->first();
```

`first()`沒有結果會失敗，`firstOrFail()` 會丟出一個例外。

如果傳入欄位名稱陣列，它會回傳這些欄位資料而非所有欄位。

#### find(id), findOrFail(id)

很像 `first()`，傳入主鍵 ID 值。`firstOrFail()` 會丟出一個例外。

```php
$contactFile = DB::table('contacts')
    ->find(5);
```

#### value()

只會從第一列的單一欄位拉出值。很像 `first()`，但如果只想要一個欄位；

```php
$newestContactEmail = DB::table('contacts')
    ->orderBy('created_at', 'desc')
    ->value('email');
```

#### count()

回傳所有符合的結果整數數量；

```php
$countVips = DB::table('contacts')
    ->where('vip', true)
    ->count();
```

#### max(), min

回傳最小最大值；

```php
$higestCost = DB::table('orders')->max('amount');
```

#### sum(), avg()

回傳特定欄位所有值的總和或平均值；

```php
$averageCost = DB::table('orders')
    ->where('status', 'completed')
    ->avg('amount');
```

## 在查詢產生器方法裡面使用 DB::raw 來邊寫原生查詢

傳入 `DB::raw()` 給 query builder 取的相同的結果；

```php
$contacts = DB::table('contacts')
    ->select(DB::raw('*, (score * 100) AS integer_score'))
    ->get();
```

## 聯結

定義聯結有時是件很痛苦的事情，可簡化這項工作的框架並不多，但 query builder 做得很好。

```php
$users = DB::table('users')
    ->join('contacts', 'users.id', '=', 'contacts.users_id')
    ->select('users.*', 'contacts.name', 'contacts.status')
    ->get();
```

`join()` 會建立一個內部連結。將多個連結一個接著一個連接起來，或使用 `leftJoin()` 來取得一個左聯結。

可將一個 closure 傳入 `join()` 方法來建立更多複雜的聯結；

```php
DB::table('users')
    ->join('contacts', function ($join) {
        $join->on('users.id', '=', 'contacts.user_id')
        ->orOn('users.id', '=', 'contacts.proxy_user_id');
    })
    ->get();
```

## 聯集


