# Controllers

Controller 主要負責捕捉 HTTP 請求的意圖，並將它傳遞給應用程式其餘的部分

## 簡單範例

```
php artisan make:controller TasksController
```

```php
// routes/web.php
Route::get('/', 'TasksController@index');
```

```php
<?php
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

