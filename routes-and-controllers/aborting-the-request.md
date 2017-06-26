# Aborting the Request

除了回傳 `view()` `redirect()` 以外，離開路由最常見的方式是 abort。

`abort()` `abort_if()` `abort_unless()` 它們可選擇接收 HTTP 狀態碼、訊息與標頭陣列等參數。

```php
Route::post('something', function (Illuminate\Http\Request $request) {
    abort(403, 'You cannot do the');
    abort_unless($request->has('magicToken'), 403);
    abort_if($request->user()->isBanned, 403)
});
```
