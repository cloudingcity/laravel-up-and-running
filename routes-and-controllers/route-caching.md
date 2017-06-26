# Route Caching

如果 App 都使用 controller 而非 closure，可使用 Laravel 的 route cache，可大大減少解析 routes/* 檔案的時間。

```
php artisan route:cache
```

刪除快取

```
php artisan route:clear
```

建議在server上在使用，每次重新 deploy 時在執行 `route:cache`。
