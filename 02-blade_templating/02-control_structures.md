# Control Structures

## If Statements

### @if

Blade 的 `@if` 會被編譯成 `<?php if ($condition): ?>`，`@else`、`@eliseif`、`@endif` 也是如此。

```html
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

### @unless @endunless

`@unless` 是一種新語法，它沒有對等的 PHP code，與 `@if` 相反。`@unless ($condition)` 同 `<?php if (!$condition)`。

```html
@unless (Auth::check())
    You are not signed in.
@endunless
```

## Loops

### @for @foreach @while

```html
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

### @forelse

`@forelse` 也是 `@foreach`，但它可以讓你在**被迭代的物件是空的**時編寫應變機制。

```html
@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse
```

### The Loop Variable

當使用 `@foreach` `@forelse` 時可使用 `$loop` 變數。

```html
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif

    @if ($loop->last)
        This is the last iteration.
    @endif

    <p>This is user {{ $user->id }}</p>
@endforeach
```

| Property         | Description                     |
|------------------|---------------------------------|
| $loop->index     | 索引從0開始                     |
| $loop->iteration | 索引從1開始                     |
| $loop->remaining | 迴圈還剩下多少個項目            |
| $loop->count     | 項目數量                        |
| $loop->first     | 項目是不是第一個，回傳boolean   |
| $loop->last      | 項目是不是最後一個，回傳boolean |
| $loop->depth     | 幾層迴圈的第幾層                |
| $loop->parent    | 參考上層迴圈                    |

## or

當不確定變數是否被設定，可能會先使用 `isset()`，Blade 有一中方便的輔助函式 `or`。
```html
{{ $title or "Default"}}
```
