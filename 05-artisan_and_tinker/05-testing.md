# Testing

## 在測試程式中呼叫 Artisan

```php
public function test_empty_log_command_empties_logs_table()
{
    DB::table('logs')->insert(['message' => 'Did something']);
    Artisan::call('logs:empty');
    $this->assertCount(0, DB::table('logs')->get());
}
```

如果想要換掉靜態介面，可改為相依關西注入 Artisan 建構式。

Artisan 靜態介面可讓你使用 `Illuminate\Contracts\Console\Kernel`，如果要避免在程式中使用靜態介面:

```php
use Illuminate\Contracts\Console\Kernel;

class NightlyCleanuup extends Job
{
    public function handle(Kernel $kernel)
    {
        $kernel->call('logs:empty');
    }
}
```