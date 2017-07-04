# Eloquent Model Mass Assignment

```php
Route::post('post', function (Request $request) {
    $newPost = Post::create($request->all()) ;
});
```

Eloquent 有種概念稱為 **大量賦值 (mass assignment)**，讓你可以將某些欄位列為
- 可用這種方式填寫的白名單(使用模型的 `$fillable` 特性)
- 不能填寫的黑名單(使用模型的 `$guarded` 特性)

```php
namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    // 停用對 author_id 欄位大量賦值
    protected $guarded = ['author_id'];
}
```

藉由對 gaurded 設定 author_id，可確保惡意用戶無法在它傳給我們的 app 表單中手動添加內容，來複寫這個欄位的值。

## 使用 $request->only() 來雙重保護

應該考慮使用 $request->only 而非 $request->all()，來讓你可以指定那些欄位傳入 model。

```php
Route::post('posts', function (Request $request) {
    $newPost = Post::create($request->only([
        'title',
        'body'
    ]));
});
```