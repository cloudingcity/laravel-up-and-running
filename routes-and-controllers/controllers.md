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

## 產生 resource controller

```
php artisan make:controller TasksController --resource
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
