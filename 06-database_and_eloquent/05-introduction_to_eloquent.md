# Introduction to Eloquent

Eloquent 是一種 ActiveRecord ORM，是一個資料庫抽向層，提供單一介面來與多種資料庫類型互動。

## 建立與定義 Eloquent

```
php artisan make:model Contact
```

會在 app/Contact.php；
```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    //
}
```

### 資料表名稱

Laravel 預設資料表名稱 "snake cases"，所以 SecondaryContact 取名為 secondary_contacts 表格。

如果要自訂可在 Model 中設定 `$table`；

```php
protected $table = 'contacts_secondary';
```

### primary key

改變主鍵名稱；

```php
protected $primaryKey = 'contact_id';
```

設為非遞增；

```php
public $incrementing = false;
```

### 時戳

Eloquent 預設每個資料表都有 `created_at`, `updated_at` 欄位，如果要停用；

```php
public $timestamp = false;
```

自訂時戳存入料庫的格式，將 $dateFormat 類別特性設為自訂的字串。這個字串會使用 PHP 的 date() 語法解析；

```php
protected $dateFormat = 'U';
```

## 使用 Eloquent 取出資料

