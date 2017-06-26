# Controllers

Controller 主要負責捕捉 HTTP 請求的意圖，並將它傳遞給應用程式其餘的部分。

## 簡單範例

```
php artisan make:controller TasksController
```

```php
// routes/web.php

Route::get('/', 'TasksController@index');
```

```php
// app/Http/Controllers/TasksController.php

namespace App\Http\Controllers;

class TasksController extends Controller
{
    public function index()
    {
        return 'Hello World';
    }
}
```

## 取得用戶輸入

### Input 靜態介面

```php
<?php

namespace App\Http\Controllers;

use App\Task;
use Illuminate\Support\facades\Input;

class TasksController extends Controller
{

    public function store()
    {
        $task = new Task;
        $task->title = Input::get('title');
        $task->description = Input::get('description');
        $task->save();

        return redirect('tasks');
    }
}
```

### Request 注入

```php
<?php

namespace App\Http\Controllers;

use App\Task;
use Illuminate\Http\Request;

class TasksController extends Controller
{

    public function store(Request $request)
    {
        $task = new Task;
        $task->title = $request->title;
        $task->description = $request->description;
        $task->save();

        return redirect('tasks');
    }
}
```

### Model Create

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Task extends Model
{
    protected $guarded = ['id'];
}
```
> 路徑: app/Task.php

```php
<?php

namespace App\Http\Controllers;

use App\Task;

class TasksController extends Controller
{

    public function store()
    {
        Task::create(request(['title', 'description']));

        return redirect('tasks');
    }
}
```

## Resource Controllers

### 產生

```
php artisan make:controller TasksController --resource
```

### 綁定
```php
Route::resource('tasks', 'TasksController');
```

### 方法

| Verb      | URI                | Action    | Route Name    |
|-----------|--------------------|-----------|---------------|
| GET       | /tasks             | index()   | tasks.index   |
| GET       | /tasks/create      | create()  | tasks.create  |
| POST      | /tasks             | store()   | tasks.store   |
| GET       | /tasks/{task}      | show()    | tasks.show    |
| GET       | /tasks/{task}/edit | edit()    | tasks.edit    |
| PUT/PATCH | /tasks/{task}      | update()  | tasks.update  |
| DELETE    | /tasks/{task}      | destroy() | tasks.destroy |

> 列出所有路由: php artisan route:list

## Route Model Binding

### Implicit Binding

要使用 Route Model Binding 為路由參數取一個該模型獨有的名稱，接著在 closure/controller method 裡 typehint 那個參數，並且使用相同變數名稱。

```php
Route::get('/tasks/{task}', function (App\Task $task) {
    return $task;
});
```

因為路由參數 ({task}) 和方法參數 ($task) 相同，Laravel 會自動解析為 Route Model Binding。

#### Customizing The Key Name

讓預設不是用 id 尋找，而是自訂 key name

```php
public function getRouteKeyName()
{
    return 'title';
}
```
> 路徑: App/Task.php

### Explicit Binding

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

## Route Caching

如果 App 都使用 controller 而非 closure，可使用 Laravel 的 route cache，可大大減少解析 routes/* 檔案的時間。

```
php artisan route:cache
```

刪除快取

```
php artisan route:clear
```

建議在server上在使用，每次重新 deploy 時在執行 `route:cache`。

## Form Method Spoofing

```html
<form action="/tasks/1" method="POST">
    {{ method_field('DELETE') }}
    <!-- or -->
    <input type="hidden" name="_method" value="DELETE">
</form>
```

## CSRF Protection

```html
<form action="/tasks/1" method="POST">
    {{ csrf_field() }}
    <!-- or -->
    <input type="hidden" name="_token" value="{{ csrf_token()}}">
</form>
```

## Redirects

### redirect()->to()

```php
Route::get('redirect', function () {
    return redirect()->to('home') ;

    // or

    return redirect('home');
});
```

### redirect()->route()

指定為路由名稱

```php
Route::get('redirect', function () {
    return redirect()->route('tasks.show', ['id' => 1]);
});
```

### redirect()->with()

當轉址到不同網頁需要傳一些資料時，可以存入session。

```php
Route::post('user/profile', function () {
   return redirect('dashboard')->with('status', 'Profile updated!') ;
});
```

之後可用 helper function session()

```html
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status')}}
    </div>
@endif
```

### Other redirect->xxx()

- `back()`: 上一頁
- `home()`: 轉址到名稱 home 的路由
- `refresh()`: 轉址到同頁面
- `away()`: 轉址到外部URL，不使用預設URL驗證
- `secure()`: 同 secure 參數設定為 true 的 to()
- `action()`: 轉址controller method，redirect()->action('TasksController@index')
- `guest()`:
- `intended()`:
