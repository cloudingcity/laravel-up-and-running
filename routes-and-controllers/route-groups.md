# 路由群組

```php
Route::group([], function () {
    Route::get('hello', function () {
        return 'Hello';
    });
    Route::get('world', function () {
        return 'World';
    });
});
```

## Middleware

限制訪客可使用的網站區域

```php
Route::group(['middleware' => 'auth'], function () {
    Route::get('dashboard', function () {
        return view('dashboard')
    });
    Route::get('account', function () {
        return view('account');
    });
});
```

將 Middleware 附加到 Controller 比在路由中定義還明確，`only()` `except()` 來定義權限。

```php
class Dashboard extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('admin-auth')
            ->only('admin');

        $this->middleware('team-member')
            ->except('admin');
    }
}
```

## Route Prefixes
