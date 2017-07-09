# Testing

## 測試被注入的非法輸入

```php
public function test_input_missing_a_title_is_rejected()
{
    $this->post('post', ['body' => 'This is the body of my post']);
    $this->assertRedirectedTo('posts/create');
    $this->assertSessionHasErrors();
    $this->assertHasOldInput();
}
```

確認收到無效的輸入之後，會將用戶與錯誤一起轉址，並正確回傳舊的輸入。

## 測試有效的輸入已被處理

```php
public function test_valid_input_should_create_a_post_in_the_database()
{
    $this->post('posts', ['title' => 'Post Title', 'body' => 'This is the body']);
    $this->seeInDatabase(['title' => 'Post Title']);
}
```
