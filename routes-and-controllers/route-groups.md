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

```php
Route::group(['prefix' => 'api'], function () {
    Route::get('/', function () {
        // /api
    });
    Route::get('/users', function () {
        // /api/users
    });
});
```

## Sub-Domain Routing

```php
Route::group(['domain' => '{account}.myapp.com']function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```

> 群組任何參數會被傳入群組內的入由方法前幾個參數

## Namespaces

免於冗長的控制器參照

```php
// App\Http\Controllers\ControllerA
Route::get('/', 'ControllerA@index');

Route::group(['namespace' => 'API'], function () {
    // App\Http\Controllers\API\ControllerA
    Route::get('api/', 'ControllerB@index');
});
```


