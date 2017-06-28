# Custom Blade Directives

假設要使用 `@if(auth()->guest())`

```php
// AppServiceProvider
public function boot()
{
    Blade::directive('ifGuest', function () {
         return '<?php if (auth()->guest()): ?>';
    });
}
```

之後用 `@ifGuest` 就可以取代 `@if(auth()->guest())`

## 自訂參數

```php
public function boot()
{
    Blade::directive('datetime', function ($expression) {
        return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
    });
}
```

相等於 `<?php echo ($var)->format('m/d/Y H:i'); ?>`

> 當發現不斷重複編寫相同條件邏輯時，就是考慮用Blade指令的時刻

