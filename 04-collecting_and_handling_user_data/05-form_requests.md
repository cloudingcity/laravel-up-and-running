# Form Requests

表單請求是一種自訂的請求類別，它的目的是為了對應 (map) 表單的提交，表單請求會負責驗證請求、授權用戶，以及在驗證失敗時轉址用戶。

## 建立表單請求

```
php artisan make:request CreateCommentRequest
```

在 `app/Http/Requests/CreateCommentRequest.php` 有一個表單請求物件產生。

每一個表單請求類別都提供一或兩個公用方法。
第一個是 `rules()`，他必須回傳這個請求的驗證規則組成的陣列。
第二個(選用) 方法是 `authorize()`，如果她回傳 `true`，代表用戶通過授權，可執行這個請求，如果是 `false`，則拒絕。

```php
<?php

namespace App\Http\Requests;

use App\BlogPost;
use Illuminate\Foundation\Http\FormRequest;

class CreateCommentRequest extends FormRequest
{
    public function authorize()
    {
        $blogPostId = $this->route('blogPost');

        return auth()->check() && BlogPost::where('id', $blogPostId)
            ->where('user_id', auth()->user()->id)->exists();
    }

    public functino rules()
    {
        return [
            'body' => 'required|max:1000'
        ];
    }
}
```

我們從一個名為 `blogPost` 的路由抓取區段。

```php
Route::post('blogPosts/{blogPost}', function() { });
```

我們將路由參數取名為 `blogPost`，所以他可以在 Request 內使用 `$this->route('參數名稱')`。

## 使用表單請求

```php
Route::post('comments', function (App\Http\Requests\CreateCommentRequest $request) {
   //
});
```

它會會驗證用戶輸入，並授權請求。如果輸入是無效的，它會採取 controller 內的 `validate()` 得作法，連同用戶輸入與適當的錯誤訊息一起將用戶轉址回上一個網頁。

當用戶未通過驗證時，Laravel 會還傳一個 403 Forbidden 錯誤，不會執行路由程式碼。