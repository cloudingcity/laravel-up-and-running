# Template Inheritance

Blade 提供一種進行模板繼承的結構，可讓view修改、擴展與納入。

## 使用 @section/@show @yield 來定義區段

```html
<!-- resources/views/layouts/master.blade.php -->

<html>
    <head>
        <title>My Site - @yield('title', 'Home Page')</title>
    </head>
    <body>
        <div class="container">
            @yield('content')
        </div>
        @section('sidebar')
            This is the master sidebar.
        @show
    </body>
</html>
```

```html
<!-- resources/views/dashboard.blade.php -->

@extends('layouts.master')

@section('title', 'Dashboard')

@section('content')
    <p>Welcome to your application dashboard!</p>
@endsection

@section('sidebar')
    @parent

    <p>This is appended to the master sidebar.</p>
@endsection
```

> `@show` v.s. `@endsection` : 當模板區段內容還沒結束時用 `@show`，反之 `@endsection`

### @extends

`@extends` 定義不能獨立顯示，而是從另一個view template **擴展** 而來的。

每一個檔案只能擴展一個別的檔案，而且`@extends` 通常都放在第一行。


### @section @endsection

定義一個區段的內容。

### @parent

用 `@parent` 來加入前面的內容，否則會覆蓋前面的內容。

### @include

`@include` 可拉入部分 view template，並選擇傳入資料

```html
<!-- resources/views/home.blade.php -->

<div>
    @include('sign-up-button', ['text' => 'See just how great it is'])
</div>
```

```html
<!-- resources/views/sign-up-button.blade.php -->
<a class="button">
    <i class="exclamation-icon"></i> {{ $text }}
</a>

```

### @each

某些情況下需要會迴圈執行一個陣列，`@include` 每一個項目的，此時可以用 `@each`。

```html
<!-- resources/views/sidebar.blade.php -->

<div class="sidebar">
    @each('partials.module', $modules, 'module', 'partials.empty.module')
</div>
```

```html
<!-- resources/views/partials/module.blade.php -->
<div class="sidebar-module">
    <h1>{{ $module->title }}</h1>
</div>
```

```html
<!-- resources/views/partials/empty.module.blade.php -->
<div class="sidebar-module">
    No modules =(
</div>
```

`@each` 參數：
1. 視圖名稱
2. 迭代的陣列
3. 變數名稱
4. 當陣列為空時要顯示的視圖
