# Testing

## 測試 POST 路由是否正常動作

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class ExampleTest extends TestCase
{
    use DatabaseTransactions;

    public function test_post_create()
    {
        $this->post('/tasks', [
            'title'       => 'My Title',
            'description' => 'My Description'
        ]);

        $this->assertDatabaseHas('tasks', [
            'title'       => 'My Title',
            'description' => 'My Description'
        ]);
    }
}
```

> use DatabaseTransactions; 讓資料庫資料測試完後刪除

## 測試 GET 路由某些文字是否顯示在網頁上

```php
<?php

namespace Tests\Browser;

use App\Task;
use Illuminate\Foundation\Testing\DatabaseMigrations;

class ExampleTest extends DuskTestCase
{
    use DatabaseTransactions;

    public function test_list_page_show_all_task()
    {
        Task::create([
            'title'       => 'My Title',
            'description' => 'My Description'
        ]);

        $this->browse(function (Browser $browser) {
            $browser->visit('/tasks')
                    ->see(['My Title']);
        });
    }
}
```

> 5.4: [Laravel Dusk](https://mattstauffer.co/blog/introducing-laravel-dusk-new-in-laravel-5-4)
