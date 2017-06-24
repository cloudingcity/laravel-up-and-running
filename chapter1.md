# Routes and Controllers

## 路由定義

```php
Route::get('/', function () {
    return 'Hello World';
});
```



\#\#\# 路由動詞



Route::get\('/', function \(\) {}\);

Route::post\('/', function \(\) {}\);

Route::put\('/', function \(\) {}\);

Route::delete\('/', function \(\) {}\);

Route::any\('/', function \(\) {}\);

Route::match\('/', function \(\) {}\);



\#\#\# 選用路由



Route::get\('users/{id?}', function \($id = 'fallbackId'\) {

    return $id;

}\);



\#\#\# 正規路由



參數符合才匹配



Route::get\('/users/{name}', function \($name\) {

    return $name;

}\)-&gt;where\('name', '\[A-z\]+'\);





Route::get\('/users/{id}/{slug}', function \($id, $slug\) {

    return 'id: ' . $id . ', slug: ' . $slug;

}\)-&gt;where\(\['id' =&gt; '\[0-9\]+', 'slug' =&gt; '\[A-Za-z\]+'\]\);





\#\#\# 路由名稱



Route::get\('members/{id}', function \($id\) {

    return route\('members.show', \['id' =&gt; $id\]\);

}\)-&gt;name\('members.show'\);



\#\# 路由群組



Route::get\(\[\], function \(\) {

    Route::get\('get', functino \(\) {

        return 'Hello';

    }\);

    Route::get\('get', functino \(\) {

        return 'World';

    }\);

}\);



\#\#\# 中介層





