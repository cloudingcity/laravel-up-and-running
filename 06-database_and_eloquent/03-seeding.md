# Sedding

Laravel seeding 很簡單。DatabaseSeeder 類別有個 *database/seeds* 資料夾，有個 `run()` 方法，當呼叫 seeder，就會執行這個方法。

與 migration 同時執行，只要加入 `--seed`:

```
php artisan migrate --seed
php artisan migrate: refresh --seed
```

分別執行:

```
php artisan db:seed
php artisan db:seed --class=VotesTableSeeder
```

## 建立 seeder

```
php artisan make:seeder ContactsTableSeeder
```

會在 *database/seeds* 目錄看到一個 ContactsTableSeeder class。

先將它加到 DatabaseSeeder 類別，當執行 seeder 時，就會執行它:

```php
// database/seeds/DatabaseSeeder.php

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
         $this->call(ContactsTableSeeder::class);
    }
}
```

DB 介面手動插入一筆紀錄:

```php
// database/seeds/ContactsTableSeeder.php

use Illuminate\Database\Seeder;

class ContactsTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('contacts')->insert([
            'name' => 'Jack',
            'email' => 'jack@gmail.com'
        ]);
    }
}
```

## Model Factories

Model Factories 定義是一多種為資料表建立偽造項目的模式。

兩種方式設定同一個資料表:

```php
$factory->define(User::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
    ];
});

$factory->define('users', function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
    ];
});
```

### 建立 Model Factories

Model Factories 定義位於 *database/factoryies/ModelFactory.php*。

簡單定義:

```php
$factory->define(App\Contact::class, function (Faker\Generator $faker) {
    return [
        'name' => 'Jack',
        'email' => 'jack@gmail.com'
    ];
});
```

用 factory() seeding 與 testing 建立 Contact 實例:

```php
// 建立一個
$contact = factory(\App\Contact::class)->create();

// 建立多個
factory(\App\Contact::class, 20)->create();
```

運用 [Facker](https://github.com/fzaninotto/Faker) 製造假資料:

```php
$factory->define(App\Contact::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->email
    ];
});
```

### 使用 Model Factories

```php
factory(\App\Post::class)->create([
   'title' => 'My Title'
]);

factory(\App\User::class, 20)->create()->each(function ($u) use ($post) {
   $post->comments()->save(factory(Comment::class)->make([
        'user_id' => $u->id
   ]));
});
```

- `make()` 會建立實例，但不會將它存到資料庫。
- `create()` 會立刻將它存置資料庫。

- **在呼叫 Model Factories 時覆寫特性**
- **使用 Model Factories 來產生多個實例**

### 定義與使用多個 Model Factories 類型

```php
$factory->define(App\Contact::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->email
    ];
});
```

如果需要新增重要人物 vip 的連絡資訊，可以定義第二種工廠類型:

```php
$factory->define(App\Contact::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->email
    ];
});

$factory->defineAs(App\Contact::class, 'vip', function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->email,
        'vip' => true
    ];
});
```

使用 `$factory->raw()` 來讓 VIP 聯絡資訊直接擴展之前的工廠:

```php
$factory->define(App\Contact::class, function (Faker\Generator $faker) {
    return [
        'name' => $faker->name,
        'email' => $faker->email
    ];
});

$factory->defineAs(App\Contact::class, 'vip', function (Faker\Generator $faker) {
    $contact = $factory->raw(App\Contact::class)

    return array_merge($contact, ['vip' => true]);
});
```

製作特定類型:

```php
$vip = factory(App\Contact::class, 'vip')->create();
$vip = factory(App\Contact::class, 'vip', 3)->create();
```
