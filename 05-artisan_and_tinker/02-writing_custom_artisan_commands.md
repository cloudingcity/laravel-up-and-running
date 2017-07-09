# Writing Custom Artisan Commands

`artisan make:command YourCommandName`，app/Console/Commands/{YourCommandName}.php 產生一個新的 Artisan 命令

第一個參數是命令類別名稱，可以選擇傳遞一個 `--command` 參數來定義終端機命令是什麼(ex. appname:action)

```
php artisan make:command WelcomeNewUsers --command=email:newusers
```

## 註冊命令

要讓這個命令生肖還要一個步驟；必續註冊它。

打開 app/Console/Kernel.php。在 $commands 陣列加入要註冊的類別。

```php
class Kernel extends ConsoleKernel
{
    /**
     * The Artisan commands provided by your application.
     *
     * @var array
     */
    protected $commands = [
        \App\Console\Commands\WelcomeNewUsers::class,
    ];
}
```

如果想要更簡單定義，可以將命令寫成 closure 而非類別，在 routes/console.php 中定義他們。

```php
// routes/console.php

Artisan::command('password:reset {userId} {--sendEmail}',
    function($userId, $sendEmail) {
        // do some stuff
    }
);
```

## 命令範例


```php
class WelcomeNewUsers extends Command
{
    public function handle()
    {
        User::signedUpThisWeek()->each(function ($user) {
            Mail::send(
                'emails.welcome',
                ['name' => $user->name],
                function ($m) use ($user) {
                    $m->to($user->email)->subject('Welcome!');
                }
            );
        });
    }
}
```

現在每執行一次 php artisan email:newusers 時，都會抓出本周註冊的每一位用戶，並寄送一封歡迎郵件給他們。

### Refactor

```php
class WelcomeNewUsers extends Command
{
    public function __construct(UserMailer $userMailer)
    {
        parent::__construct();
        $this->userMailer = $userMailer;
    }

    public function handle()
    {
        $this->userMailer->welcomeNewUsers();
    }
}
```

### 保持簡單

你可以在其餘的程式碼呼叫 Artisan 命令，所以你可以使用他們來封裝應用邏輯區塊。

Laravel 文件建議你將服務注入你的命令，而非將應用邏輯包入服務類別。主控台命令是類似控制器的東西:他們不是網域類別，而是交通警察，只負責將被傳入的請求路由到正確的行為。

## 引數與選項

可以使用明確、簡單的語法來為 Artisan 命令添加引數與選項。

```php
protected $signature = 'password:reset {userId} {--sendEmail}';
```

### 引數、必要、選用與 (或) 具有預設值

定義一個必要的引數:

```
password:reset {userId}
```

引數選用:

```
password:reset {userId?}
```

選用並提供預設值:

```
password:reset {userId=1}
```

### 選項、必要的值、預設值，與快捷方式

選項類似引數，但要在前面加上 `--`，而且在使用時可以沒有值:

```
password:reset {userId} {--sendEmail}
```

如果選項需要一個值:

```
password:reset {userId} {--password=}
```

如果選項需要一個預設值:

```
password:reset {userId} {--quene=default}
```

### 陣列引數與選項

接收陣列輸入:

```
password:reset {userIds*}
// php artisan password:reset 1 2 3

password:reset {--ids=*}
// php artisan password:reset --ids=1 --ids=2 --ids=3
```

> 陣列引數必須為最後一個引數或選項

## 輸入說明

`artisan help` 顯示的詳細資訊

```php
protected $signature = 'password:reset {userId : The ID of the user} {--sendEmail : Whether to send user an email}';
```

## 使用輸入

可在 handle() 中使用

### argument()

回傳引數陣列

```
// define 'password:reset {userId}'
php artisan password:reset 5

// $this->argument() return array
[
    "command" => "password:reset",
    "userId" => "5"
]

// $this->argument('userId') return string
"5"
```

### option()

回傳選項陣列，包括預設值為 false or null。

```
// define "password:reset {--userId=}"
php artisan password:reset --userId=5

// $this->option return array
[
    "userId" => "5"
    "help" => false
    "quiet" => false
    "verbose" => false
    "version" => false
    "ansi" => false
    "no-ansi" => false
    "no-interaction" => false
    "env" => null
]

// $this->option('userId') return string
"5"
```

## 提示

### ask()

提示用戶輸入自由格式文字:

```php
$email = $this->ask('What is your email address ?');
```

### secret()

提示用戶輸入自由格式文字，但使用星號來隱藏輸入:

```php
$password = $this->secret('What is the DB password?');
```

### confirm()

提示用戶輸入 yes/no 答案，並回傳 boolean:

```php
if ($this->confirm('Do you want to truncate the tables?')) {
    //
}
```

除了 Y y 以外的答案都為 no

### anticipate()

提示用戶輸入自由格式，並提供自動完成建議。

```php
$album = $this->anticipate('What is the best album ever?', [
    "The Joshua Tree", "Pet Sounds", "What's Going On"
]);
```

### choice()

提示用戶選擇所提供的選項之一。最後一個參數是當用戶未選擇時的預設選項:

```php
$winner = $this->choice(
    'Who is the best football team?',
    ['Gators', 'Wolverines'],
    0
);
```

or

```php
$winner = $this->choice(
    'Who is the best football team?',
    ['gators' => 'Gators', 'wolverines' => 'Wolverines'],
    'gators'
);
```

## 輸出

當執行命令時，要給用戶訊息

```php
$this->info('Your command has run successfully.');
```

- info(): 綠色
- comment(): 橘色
- question(): 亮藍綠色
- error(): 亮紅色
- line(): 無色