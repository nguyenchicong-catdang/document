# link
config/filesystems.php

```php
'links' => [
        public_path('storage') => storage_path('app/public'),
        public_path('uploads') => __DIR__ . '/../wp-learn/uploads',
    ],
```
