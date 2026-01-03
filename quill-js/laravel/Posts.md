# Models/Posts.php
```php
    protected $fillable = [
        'title',
        'content'
    ];
    protected function casts(): array
    {
        return [
            'content' => 'json',
        ];
    }

```
