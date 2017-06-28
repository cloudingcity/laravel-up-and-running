# Testing

## 測試 view 可顯示某些內容

```php
// EventsTest.php
public function test_list_page_show_all_events()
{
    $event1 = factory(Event::class)->create();
    $event2 = factory(Event::class)->create();
    
    $this->visit('events')
        ->see($event1->title)
        ->see($event2->title);
}
```

## 測試 view 已被傳入某些內容

```php
// EventsTest.php
public function test_list_page_show_all_events()
{
    $event1 = factory(Event::class)->create();
    $event2 = factory(Event::class)->create();
    
    $this->visit('events');
    
    $this->assertViewHas('events', Event::all());
    $this->assertViewHasAll([
        'events' => Event::all(),
        'title' => 'Events Page'
    ]);
    $this->assertViewMissing('dogs');
}
```

## 將 closure 傳入 assertViewHas()

```php
// EventsTest.php
public function test_list_page_show_all_events()
{
    $event1 = factory(Event::class)->create();
    
    $this->visit('events/' . $event1->id);
    
    $this->assertViewHas('events', function ($event) use ($event1) {
        return $event->id === $event1->id;
    });
}
```