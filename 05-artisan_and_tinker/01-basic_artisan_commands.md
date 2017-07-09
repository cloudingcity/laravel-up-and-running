# Basic Artisan Commands

- `help`: 例如 `php artisan help commandName`。
- `clear-compiled`: 會移除 Laravel 已編譯的類別檔案，就像內部快取；當發現有些事情不對勁，而且不知道原因時，第一種處理方式就是執行他。
- `down`: 會將應用程式設為 **維護模式**，讓你可以修正錯誤、執行 migration 或其他事情。
- `up`: 會將應用程式從維護模式恢復。
- `env`: 會顯示 Laravel 正在持行哪一個環境；相同於 `app()->environment()`。
- `migrate`: 執行所有資料庫 migration。
- `optimize`: 將應用程式優化，讓他有更好的效能，做法是將 PHP 核心類別快取至 bootstrap/cache/compile.php。
- `serve`: 會在 localhost:8000 啟動一個 PHP Server (可用 `--host` `--port` 來自訂 host 和 post)。
- `tinker`: 可啟動 Tinker REPL。

## Options

這些選項是每次執行 Artisan 命令時可傳入的，也可以讓自動化程序使用:

- `-q`: 會禁止所有輸出。
- `-v` `-vv` `-vvv`: 三個輸出的詳細等級(一般、詳細、除錯)。
- `--no-interaction`: 不會詢問任何互動問題，所以它不會中斷執行它的自動化程序。
- `--env`: 可讓你定義 Artisan 命令應在哪個環境運作(local、production...)。
- `--version`: 會顯示應用程式在哪個 Laravel 版本上運行。

## 群組化的命令

其餘現成的命令都是以環境來分組

### app

只包含 `app:name`，可讓預設的頂層命名空間 App\ 的每一個換成自訂的 namespace。`php artisan app:name MyApp`。

### auth

只有 `auth:clear-resets`，會清除資料庫內所有過期的密碼重置 token。

### cache

`cache:clear` 會清除快取，`cache:table` 會建立一個資料庫 migration，如果準備使用資料庫快取驅動程式的話。

### config

`config:cache` 會快取組態設定來讓查詢更快速；可以使用 `config:clear` 清除快取。

### db

`db:seed` Seed the database with records。

### event

`event:generate` 會根據 EventServiceProvider 內定義來建立遺失的事件與事件監聽檔案。

### key

`key:generate` 會在你的 .env 檔案裡面建立一個隨雞的 app 加密 key。

### make

`make:auth` Scaffold basic login and registration views and routes

`make:migration create_posts_table --create=posts`

### migrate

- `migrate:install` 可建立 migration table(追蹤已被職執行的 migration)
- `migrate:reset` Rollback all database migrations
- `migrate:refresh` Reset and re-run all migrations
- `migrate:rollback` 復原上一個 migration
- `migrate:status` 檢查 migration 狀態

### notifications

`notifications:table` 產生一個可建立資料庫通知表格的 migration

### queue

基本概念: 將工作推往遠端的佇列，讓工人一個接著一個執行。

- `queue:listen` 開始監聽佇列
- `quene:table` 可建立資料庫支持的佇列
- `quene:flush` 清除所有失敗的佇列工作

### route

- `route:list` 列出所有路由
- `route:cache` 快取路由，讓查找更快速
- `route:clear` 清除快取

### schedule

須設定系統 cron 來每分鐘執行 `schedule:run`

```
* * * * * php /home/myapp.com/artisan schedule:run >> /dev/null 2>&1
```

### session

`session:table` Create a migration for the session database table

### storage

`storage:link` 會從 public/storage 建立符號連結到 storage/app/public。這在 Laravel app 中是常見的規範，可方便將用戶上船的東西(或其他最後被放在 storage/app 的檔案) 方在他們可以用公共URL存取的地方。

### verdor

`verdor:publish` Publish any publishable assets from vendor packages

### view

Laravel view 可自動快取，如果發現卡住了可執行 `view:clear` 來清楚快取

