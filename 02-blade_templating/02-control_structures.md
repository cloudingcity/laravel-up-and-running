# Control Structures

## 條件

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

## 迴圈

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
