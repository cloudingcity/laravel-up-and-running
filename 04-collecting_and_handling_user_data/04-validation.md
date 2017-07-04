# Validation

## 在 Controller 內使用 ValidatesRequests 的 validate()

```php
// app/Http/Controllers/TasksController.php
public function store(Request $request)
{
    $this->validate($request, [
        'title' => 'required|max:125',
        'description' => 'required'
    ]);

    // do some stuff
}
```

如果資料無效，他會丟出 ValidationException，

如果請求是 Ajax (或是請求以 JSON 回應)，例外會建立一個 JSON 回應，裡面含有驗證錯誤。
如果不是，會連同所有的用戶輸入與驗證錯誤一起回傳到一個轉址，回到上一個網頁。

## 手動驗證

如果不是在 controller 內工作，可以手動建立一個。
```php
Route::post('tasks', function (Illuminate\Http\Request $request) {
   $validator = Validator::make($request->all(), [
       'title' => 'required',
       'description' => 'required'
   ]);

   if ($validator->fails()) {
       return redirect('tasks/create')
           ->withErrors($validator)
           ->withInput();
   }
});
```

## 顯示驗證錯誤訊息

Controller 的 `validate()` 方法 (與它轉址的 `withErrors()` 方法) 會將任何錯誤填入 session。

每當你仔入 view ，都可以使用 `$errors` 變數。

```html
@if ($errors->any())
    <ul id="errors">
        @foreach($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
    </ul>
@endif
```
