# Introduction to Eloquent

Eloquent 是一種 ActiveRecord ORM，是一個資料庫抽向層，提供單一介面來與多種資料庫類型互動。

## 建立與定義 Eloquent

```
php artisan make:model Contact
```

會在 app/Contact.php:
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

如果要自訂可在 Model 中設定 `$table`:

```php
protected $table = 'contacts_secondary';
```

### primary key

改變主鍵名稱:

```php
protected $primaryKey = 'contact_id';
```

設為非遞增:

```php
public $incrementing = false;
```

### 時戳

Eloquent 預設每個資料表都有 `created_at`, `updated_at` 欄位，如果要停用:

```php
public $timestamp = false;
```

自訂時戳存入料庫的格式，將 $dateFormat 類別特性設為自訂的字串。這個字串會使用 PHP 的 date() 語法解析:

```php
protected $dateFormat = 'U';
```

## 使用 Eloquent 取出資料

取得所有:

```php
$allContacts = Contact::all();
```

篩選:

```php
$vipContacts = Contact::where('vip', true)->get();

$newestContacts = Contact::orderBy('created_at', 'desc')
    ->take(10)
    ->get();
```

初始的靜態介面名稱之後，使用的只不過是 Laravel Query Builder。

### 取得一筆資料

```php
// ContactController
public functino show($contactId)
{
    return view('contacts.show')
        ->with('contact', Contact::findOrFail($contactId));
}
```

### 取得多筆資料

`get()` 與 Eloquent 合作的方式，就相它與一班的 query bulider 方式一樣建立一個查詢指令，並在結尾呼叫 `get()` 來取得結果:

```php
$vipContacts = Contact::where('vip', true)->get();
```

但是，有一種只能在 Eloquent 使用的方法 `all()`，用途通常是取得表中未經篩選的資料串列:

```php
$contacts = Contact::all();
```

> 建議以 `get()` 取代 `all()`。`Contact::all()` 和 `Contact::get()` 回應相同，但如果修改查詢時(例如 where())，`all()` 就不能用了

### 使用 chunk() 來將回應分為區塊

當一次處理大量紀錄時，可能會遇到記憶體不足或鎖定的問題。Laravel 可以將請求拆成較小的區塊，批次處理他們，減少大型的請求所需的記憶負擔:

```php
Contact::chunk(100, function ($contacts) {
    foreach ($contacts as $contact) {
        // do something
    }
});
```

### Aggregates

```php
$count = Contact::where('vip', true)->count();
$count = Contact::sum('votes');
$count = Contact::avg('skill_level');
```

## 使用 Eloquent 來插入更新

插入更新是 Eloquent 與 query builder 語法不同的地方之一。

### 插入

```php
$contact = new Contact;
$contact->name = 'Ken';
$contact->email = 'ken@gmail.com';
$contact->save();

// or

$contact = new Contact([
    'name' => 'Ken',
    'email' => 'ken@gmail.com'
]);
$contact->save();
```

也可以傳遞一個陣列給 Model::create() 來產生相同的輸出:

```php
$contact = Contact::create([
    'name' => 'Ken',
    'email' => 'ken@gmail.com'
]);
```

當你傳入陣列時(無論 new Model()、Model::create()、Model::update())，都必須被批准用於 "大量賦值"。

### 更新

更新紀錄插入紀錄很相似。可以取得特定實例、改變他的特性，接著儲存；或者可以發出一個呼叫，並傳遞一個更新後的特性陣列:

```php
$contact = Contact::find(1);
$contact->email = 'natalie@parkfamily.com';
$contact->save();
```

會修改 `updated_at` 欄位。

透過傳遞一個陣列給 `update()` 方法來更新一或多筆的 Eloquent 紀錄:

```php
Contact::where('created_at', '<', Carbon::now()->subYear())
    ->update(['longevity' => 'ancient']);

// or

$contact = Contact::find(1);
$contact->update(['longevity' => 'ancient']);
```

### 大量賦值 Mass Assignment

除非你已經在模型中定義那些欄位是 "可填入" 的，否則他們都沒有實際效果。

這種做法是為了避免(惡意的)用戶不小心設定你不想要改變的欄位值。

```php
// ContactController
public function update(Contact $contact, Request $request)
{
    $contact->update($request->all());
}
```

使用 Eloquent 的 fillable 和 guarded 特性來定義大量賦值:

```php
class Contact
{
    protected $fillable = ['name', 'email'];

    // or

    protected $guarded = ['id', 'created_at', 'updated_at', 'owner_id'];
}
```

### firstOrCreate(), firstOrNew()

有時候想要求應用程式: 如果沒有就幫我建立他。

```php
$contact = Contact::firstOrCreate(['email' => 'luis.ramos@mycam.com']);
```

- `firstOrCreate()`: 會將實例持久保存於資料庫並回傳他。
- `firstOrNew()`: 只會回傳他，不會保存。

## 使用 Eloquent 來刪除資料

使用 Eloquent 刪除資料非常類似更新，但當你使用 soft delete ，可以查看被刪除的項目，或回復他。

### 一般刪除

```php
$contact = Contact::find(5);
$contact->delete();
```

如果只有ID，就不需要為了刪除一個實例兒去尋找他；可以傳遞一個ID或陣列給 `destroy()` 方法來直接刪除它:

```php
Contact::destroy(1);

// or

Contact::destroy([1, 5, 7]);
```

刪除查詢指令的所有結果:

```php
Contact::where('updated_at', '<', Carbon::now()->subYear())->delete();
```

### 虛刪除 soft delete

