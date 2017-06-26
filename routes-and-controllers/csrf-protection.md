# CSRF Protection

```html
<form action="/tasks/1" method="POST">
    {{ csrf_field() }}
    <!-- or -->
    <input type="hidden" name="_token" value="{{ csrf_token()}}">
</form>
```
