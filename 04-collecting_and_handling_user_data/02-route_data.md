# Route Data

有三種方式可以從 URL 取得資料:
- 透過 Request static interface
- 路由參數
- Request Object

## 從 Request

URL: `/users/1`

- `$request->segments()`: ['users', '1']
- `$request->segment(1)`: return 'users'
- `$request->segment(2)`: return 1

## 從路由參數

```php
Route::get('/users/{id}', function($id) {
    // $id
});
```
