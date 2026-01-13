# Redis
```.env
CACHE_STORE=redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

## Cách Cache một sản phẩm (Dùng trong Controller)

```php
use App\Models\WCProduct;
use Illuminate\Support\Facades\Cache;

public function show($id)
{
    // Cache lại trong 1 tiếng (3600 giây) với key duy nhất cho từng ID
    $product = Cache::remember("product_detail_{$id}", 3600, function () use ($id) {
        // Chỉ những gì nằm trong function này mới tốn thời gian truy vấn DB
        return WCProduct::with('meta')->find($id);
    });

    return view('product.detail', compact('product'));
}
```

## Vấn đề quan trọng: Khi nào thì xóa Cache
```php
// Trong App\Models\WCProduct.php
protected static function booted()
{
    static::saved(function ($product) {
        // Khi sản phẩm được lưu/cập nhật, xóa cache của nó ngay
        Cache::forget("product_detail_{$product->ID}");
    });
}
```
## Kiểm tra thực tế
```php
// Lần 1: Tốn khoảng 20-50ms (Truy vấn DB)
$p = Cache::remember('test_p', 60, fn() => App\Models\WCProduct::first());

// Lần 2: Tốn ~1-2ms (Lấy từ Redis)
$p = Cache::remember('test_p', 60, fn() => App\Models\WCProduct::first());
```