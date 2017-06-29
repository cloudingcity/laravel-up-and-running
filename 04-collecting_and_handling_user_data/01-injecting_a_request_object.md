# Injecting a Request Object

在 Laravel 中，取得用戶資料最常見的工具是注入一個 `Illuminate\Http\Request` 物件的實例。取得用戶在網站上以任何方式輸入的資料，包刮 POST、貼出的 JSON 、GET 與 URL片段。

> 此外還有 request() helpers、 Request static interface 可用，都公開了相同的方法。

```php
Route::post('form', function (Illuminate\Http\Request $request) {
   // $request->etc();
});
```

## $request->all()

`$request->all()` 會給一個陣列，裡面含有用戶所提供的所有輸入。

```html
<form method="post" action="/post-route?utm=123">
    {{ csrf_field() }}
    <input type="text" name="firstName">
    <input type="submit" value="submit">
</form>
```

```php
Route::post('/post-route', function(Request $request) {
    dd($request->all());
});
```

輸出:
```
[
    "_token" => "I2BrV8sp9Ndg2lT1i9oUWEXrIxghaCIaLMDHkgUO"
    "firstName" => "Jack"
    "utm" => "123"
]
```

## $request->except()

`$request->except()` 輸出與 `$request->all()` 相同，差別在於可以選擇排出欄位。

```php
Route::post('/post-route', function(Request $request) {
    dd($request->except('_token'));
});
```

輸出:
```
[
    "firstName" => "Jack"
    "utm" => "123"
]
```

## $request->only()

與 `$request->except()` 相反。

```php
Route::post('/post-route', function(Request $request) {
    dd($request->only('firstName', 'utm'));
});
```

輸出:
```
[
    "firstName" => "Jack"
    "utm" => "123"
]
```

## $request->has()

可以使用 `$request->has()` 來判斷是否有輸入。

```php
if ($request->has('utm')) {
    // do some stuff
}
```

## $request->exists()

與 `$request->has()` 差異在於以不同方式處理空值。

| $request           | key 存在，值是空 |
|--------------------|------------------|
| $request->has()    | false            |
| $request->exists() | true             |

## $request->input()

取得單一欄位的值。第二個參數為預設值。

```php
Route::post('/post-route', function(Request $request) {
    dd($request->input('firstName', 'anonymous'));
});
```

## 陣列輸入

Laravel 提供方便的輔助程式來處理被輸入的陣列資料。

```html
<form method="post" action="/post-route?utm=123">
    {{ csrf_field() }}
    <input type="text" name="employees[0][firstName]">
    <input type="text" name="employees[0][lastName]">
    <input type="text" name="employees[1][firstName]">
    <input type="text" name="employees[1][lastName]">
    <input type="submit" value="submit">
</form>
```

```php
Route::post('/post-route', function(Request $request) {
    $employeeZeroFirstName = $request->input('employees.0.firstName');
    $allLastNames = $request->input('employees.*.lastName');
    $employeeOne = $request->input('employees.1');
});
```

輸出:
```
// 填入 'A', 'a', 'B', 'b'
$employeeZeroFirstName = 'A';
$allLastNames = ['a', 'b'];
$employeeOne = ["firstName" => "B", "lastName" => "b"];
```

## JSON 輸入 (與 $request->json())

```
POST /post-route HTTP/1.1
Content-Type: application/json

{
    "firstName": "Jack",
    "lastName": "Schome",
    "spouse": {
        "firstName": "Jill",
        "lastName": "Schome"
    }
}
```

```php
Route::post('post-route', function (Request $request) {
    $firstName = $request->input('firstName') ;
    $spouseFirstname = $request->input('spouse.firstName');
});
```

$request->input() 很聰明，可以從 GET、POST、JSON拉入用戶資料。

那為什麼還要用 $request->json() ?
- 讓專案其他成員更清楚預期的資料來源
- 如果 POST 沒有正確的 application/json 標頭，`$request->input()` 就不會以JSON 來解析他
