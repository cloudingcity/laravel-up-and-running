# Views

```php
Route::get('/', function() {
   return view('home');
});
```

> 路徑: resources/views/home.blade.php or home.php

## Passing Data To Views

```php
Route::get('/', function() {
   return view('home', ['name' => 'Jack']);
});

// same as

Route::get('/', function() {
   return view('home')->with('name', 'Jack');
});
```

## Sharing Data With All Views

共用相同變數

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        view()->share('name', 'Jack');
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}

```

> 路徑: app/Providers/ServiceProvider

