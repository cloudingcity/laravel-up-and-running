# Route Model Binding

## Implicit Binding

要使用 Route Model Binding 為路由參數取一個該模型獨有的名稱，接著在 closure/controller method 裡 typehint 那個參數，並且使用相同變數名稱。

```php
Route::get('/tasks/{task}', function (App\Task $task) {
    return $task;
});
```

因為路由參數 ({task}) 和方法參數 ($task) 相同，Laravel 會自動解析為 Route Model Binding。

### Customizing The Key Name

讓預設不是用 id 尋找，而是自訂 key name

```php
public function getRouteKeyName()
{
    return 'title';
}
```
> 路徑: App/Task.php

## Explicit Binding

自訂路由模型綁定，在`App\Providers\RouteServiceProvider` `boot()` 中定義

```php
public function boot()
{
    parent::boot();

    Route::model('user', App\User::class);
}
```

```php
Route::get('profile/{user}', function (App\User $user) {
    //
});
```

當一個路由有名為 {user} 的參數時，就會自動解析回傳 User instance。
