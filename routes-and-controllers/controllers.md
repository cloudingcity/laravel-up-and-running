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
