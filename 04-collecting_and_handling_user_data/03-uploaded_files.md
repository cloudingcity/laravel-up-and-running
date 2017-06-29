# Uploaded Files

回傳 `Symfony\Component\HttpFoundation\File\UploadedFile` 的實例。

```html
<form method="post" action="/file" enctype="multipart/form-data">
    {{ csrf_field() }}
    <input type="text" name="name">
    <input type="file" name="profile_picture">
    <input type="submit" value="submit">
</form>
```

```php
Route::post('/file', function(Request $request) {
    dd($request->all());
});
```

輸出:
```
[
    "_token" => "WMvno1Jxtb4OtKEZ9MsXPy33BUwFFbUWdLIyPy4i"
    "name" => "picture"
    "profile_picture" => UploadedFile {}
]
```

## $request->file()

```php
Route::post('/file', function(Request $request) {
    if ($request->hasFile('profile_picture')) {
        dd($request->file('profile_picture'));
    }
});
```

輸出:
```
UploadedFile {}
```

`$request->hasFile()`: 檢查是否上傳檔案
`$request->hasFile()->isValid()`: 檢查是否成功上傳


## 常見上傳檔案流程

第一個參數是本地目錄，第二個參數是儲存檔案的磁碟，指定改名改用 `storeAs()`。

> 存在 `/storage`

```php
if ($request->hasFile('profile_picture')) {
    $path = $request->profile_picture->store('profiles', 's3');
    auth()->user()->profile_picture = $path;
    auth()->user()->save();
}
```
