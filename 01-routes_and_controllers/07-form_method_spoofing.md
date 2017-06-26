# Form Method Spoofing

```html
<form action="/tasks/1" method="POST">
    {{ method_field('DELETE') }}
    <!-- or -->
    <input type="hidden" name="_method" value="DELETE">
</form>
```
