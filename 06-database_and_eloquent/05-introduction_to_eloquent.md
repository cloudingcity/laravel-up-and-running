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

soft delete 會將資料庫的資料列標記為已刪除，但不會真的在資料庫中刪除他們。

自行編寫 soft delete 最困難的部分在於，你寫的 **每一個查詢指令** 都必須排除被 soft delete 的資料。

Eloquent 的 soft delete 功能需要在資料表加入一個 deleted_at 欄位。當啟用 soft delete 時，你寫過的每一個查詢都被限制忽略被 soft delete 的資料。

> 建議必要的時候再使用 soft delete

#### 啟用虛刪除

啟用 soft deleted 需要做三件事:
1. 在 migration 中增加 `deleted_at` 欄位
2. 在 Model 中匯入 SoftDeletes traits
3. 為 $dates 加入 `deleted_at` 欄位

```php
Schema::create('contacts', function (Blueprint $table) {
    $table->softDeletes();
});
```

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Contact extends Model
{
    use SoftDeletes;

    protected $dates = ['deleted_at'];
}
```

現在每一個 `delete()`, `destroy()` 都會將資料列的 `deleted_at` 欄位設為目前的日期與時間，而非刪除那一列。之後所有的指令都會將那一列排除在結果之外。

#### 納入虛刪除項目的指令

加入被虛刪除的項目:

```php
$allHistoricContacts = Contact::withTrashed()->get();
```

使用 `trashed()` 來查看特定實例是否已被虛刪除:

```php
if ($contact->trashed()) {

}
```

只取得被虛刪除的項目:

```php
$deletedContacts = Contact::onlyTrashed()->get();
```

恢復被虛刪除的項目:

```php
$contact->restore();

// or

Contact::onlyTrashed()->where('vip', true)->restore();
```

強制刪除被虛刪除的項目:

```php
$contact->forceDelete();

// or

Contact::onlyTrashed()->forceDelete();
```

## 範圍 Query Scopes

Eloquent 的區域與全域範圍可讓你地應預設的篩選器，可以在每次查詢 Model 時，或每次使用特定方法戀來查詢時使用它。

### 區域範圍

```php
$activeVips = Contact::where('vip', true)->where('trial', false)->get();
```

不斷編寫這個查詢會覺得很麻煩，這類的定義也會片不在我們的應用程式中。

寫成這樣:
```php
$activesVips = Contact::activeVips()->get();
```

在 Contact 類別定義:

```php
class Contact
{
    public function scopeActiveVips($query)
    {
        return $query->where('vip', true)->where('trial', false);
    }
}
```

要定義區域範圍，要在 Eloquent 類別中加入一個以 scope 開頭的方法，接著在首字母大寫的範圍名稱。這個方法會接受一個查詢產生器，並回傳。

也可以定義接收參數的範圍:

```php
class Contact
{
    public function scopeStatus($query, $status)
    {
        return $query->where('status', $status);
    }
}
```

```php
$friends = Contact::status('friend')->get();
```

### 全域範圍

虛刪除只有在你讓模型內的每一個指令都忽略被虛刪除的項目時才有效

定義全域範圍的方式有兩種:

1. 使用 closure
2. 使用整個類別

這兩種方法都需要再 Model boot() 方法內註冊定義的範圍。


#### 使用 closure

```php
class Contact extends Model
{
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope('active', function (Builder $builder) {
            $builder->where('active', true);
        });
    }
}
```

加入一個名為 active 的全域範圍，這個模型的每一個指令的範圍都會被限制成 active 為 true。

#### 使用整個類別

建立一個 Illuminate\Database\Eloquent\Scope 類別，代表它將會有一個 `apply()` 方法:

```php
namespace App\Scopes;

use Illuminate\Database\Eloquent\Scope;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class ActiveScope implements Scope
{
    public function apply(Builder $builder, Model $model)
    {
        return $builder->where('active', true);
    }
}
```

要將這個範圍套用到模型，`boot()` 方法，並使用 static 類別來呼叫 `addGlobalScope()`:

```php
use App\Scopes\ActiveScope;
use Illuminate\Database\Eloquent\Model;

class Contact extends Model
{
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope(new ActiveScope);
    }
}
```

#### 移除全域範圍