# Custom Responses


## Response Objects

```php
Route::get('home', function () {
    return response('Hello World', 200)
               ->header('Content-Type', 'text/plain');
});
```

## Attaching Headers To Responses

```php
return response($content)
        ->header('Content-Type', $type)
        ->header('X-Header-One', 'Header Value')
        ->header('X-Header-Two', 'Header Value');

// same as

return response($content)
        ->withHeaders([
            'Content-Type' => $type,
            'X-Header-One' => 'Header Value',
            'X-Header-Two' => 'Header Value',
        ]);
```

## JSON Responses

```php
return response()->json([
    'name' => 'Abigail',
    'state' => 'CA'
]);
```

## File Downloads

```php
return response()->download($pathToFile);

return response()->download($pathToFile, $name, $headers);

return response()->download($pathToFile)->deleteFileAfterSend(true);
```
