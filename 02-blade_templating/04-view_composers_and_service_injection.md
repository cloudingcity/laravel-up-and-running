# View Composers and Service Injection

## 使用視圖組件來將資料綁定視圖

view composer，它可以讓你**無論何時，當某個視圖被載入時，它都會收到某些資料**，不用一直對路由傳遞資料。

### 每個路由都傳入資訊看板的資料

```php
Route::get('home', function () {
   return view('home')
       ->with('posts', Post::recent());
});

Route::get('about', function () {
   return view('about')
       ->with('posts', Post::recent());
});
```

## 全域共用變數


```php
// App/Providers/AppServiceProvider

public function boot()
{
    view()->share('posts', Post::recent());
}
```

`view()->share()` 讓每個view都共用這個變數。

## 採用 Closure 的 view composer

```php
view()->composer('partials.sidebar', function ($view) {
   $view->with('posts', Post::recent());
});
```

