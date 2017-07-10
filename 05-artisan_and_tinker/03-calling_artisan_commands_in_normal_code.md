# Calling Artisan Commands in Normal Code

雖然 Artisan 的設計是為了在 CLI 上執行的，但也可以在其他程式碼呼叫它們。

- `Artisan::call()` 來呼叫一個命令(他會回傳命令的結束碼(exit code))。

- `Artisan::queue()` 來將命令加入佇列。


這兩種方式都會接收兩個參數:

1. 終端機命令(password:reset)
2. 傳入的參數陣列

```php
Route::get('/test-artisan', function() {
   $exitCode = Artisan::call('password:reset', [
       'userId' => 15, '--sendEmail' => true
   ]);
});
```

也可使用 `$this->call` 來從其他命列呼叫 Artisan 命令(與 Artisan::call() 相同，或使用 $this->callSilent 也依樣，不過他會取消所有輸出)

```php
public function handle()
{
    $this->callSilent('password:reset', [
        'userId' => 15
    ]);
}
```

最後可以注入 `Illuminate\Contracts\Console\Kernel` 合約實例，並使用它的 call() 方法。