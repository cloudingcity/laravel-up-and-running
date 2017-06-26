# Echoing Data

使用 `{{ }}` 包覆要 echo 的 PHP 段落

```html
{{ $variable }}

// same as

<?= $varible ?>
```

預設情況下 Blade 會使用 `htmlentities()` 來顯示回應，防止惡以腳本插入攻擊，如果不想使用可改用 `{!! !!}`

```html
{{ $variable }}

// same as

<?= htmlentities($varible) ?>
```
