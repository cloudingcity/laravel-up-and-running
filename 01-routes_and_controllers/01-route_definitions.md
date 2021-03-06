# Route Definitions


```php
Route::get('/', function () {
    return 'Hello World';
});
```

> 路徑: routes/web.php

## 路由處理

將請求傳到 App\Http\Controllers\WelcomeController Controller 的 index() 方法

```php
Route::get('/', 'WelcomeController@index');
```

## 路由參數

```php
Route::get('user/{id}/friends', function ($id) {
   //
});
```
> {id} $id 可以使用不同名稱，不過為了好維護請取相同名稱

## 路由動詞

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

```php
Route::match(['get', 'post'], '/', function () {
    //
});

Route::any('foo', function () {
    //
});
```

## 選用路由

```php
Route::get('users/{id?}', function ($id = 'fallbackId') {
    return $id;
});
```

## 正規路由

參數符合才匹配

```php
Route::get('/users/{name}', function ($name) {
    return $name;
})->where('name', '[A-z]+');


Route::get('/users/{id}/{slug}', function ($id, $slug) {
    return 'id: ' . $id . ', slug: ' . $slug;
})->where(['id' => '[0-9]+', 'slug' => '[A-Za-z]+']);
```

## 路由名稱

```php
Route::get('members/{id}', function ($id) {
    return route('members.show', ['id' => $id]);
})->name('members.show');
```
