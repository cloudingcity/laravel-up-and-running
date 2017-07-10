# Tinker

Tinker 是一種 REPL，讀取-求值-輸出(read-evl-print loop)。

REPL 會停工一個提示，類似命令列提示，它會模仿應用程式 "等待" 的狀態。將命令輸入REPL，按下Return，接著等待你輸入的東西被執行，接著印出回應。

```
$ php artisan tinker

>>> $user = new App\User;
=> App\User {}
>>> $user->email = 'clouding@gmail.com';
=> "clouding@gmail.com"
>>> $user->password = bcrypt('secret');
=> "$2y$10$4HbIDm0BabLZIlIINnoQyeO3K0WsgDPPHw7EbgiVzGZCpfxSSLqLW"
>>> $user->save();
=> true
```

Tinker 是由 [Psy Shell](http://psysh.org/) 提供支援的。