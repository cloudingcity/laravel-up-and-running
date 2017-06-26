# Redirects

## redirect()->to()

```php
Route::get('redirect', function () {
    return redirect()->to('home') ;

    // or

    return redirect('home');
});
```

## redirect()->route()

指定為路由名稱

```php
Route::get('redirect', function () {
    return redirect()->route('tasks.show', ['id' => 1]);
});
```

## redirect()->with()

當轉址到不同網頁需要傳一些資料時，可以存入session。

```php
Route::post('user/profile', function () {
   return redirect('dashboard')->with('status', 'Profile updated!') ;
});
```

之後可用 helper function `session()`

```html
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status')}}
    </div>
@endif
```

## redirect()->withInput()

有時候會需要回傳使用者輸入

```php
Route::post('user/profile', function () {
   // Validate the request

   return back()->withInput();
});
```

用 `old()` 取得舊輸入

```php
<input type="text" name="username" value="{{ old('username') }}">
```

## redirect()->withErrors()

自動與轉址目的網頁 view 分享 `$error` 變數

## Other redirect->xxx()

- `back()`: 上一頁
- `home()`: 轉址到名稱 home 的路由
- `refresh()`: 轉址到同頁面
- `away()`: 轉址到外部URL，不使用預設URL驗證
- `secure()`: 同 secure 參數設定為 true 的 to()
- `action()`: 轉址controller method，redirect()->action('TasksController@index')
- `guest()`:
- `intended()`:
