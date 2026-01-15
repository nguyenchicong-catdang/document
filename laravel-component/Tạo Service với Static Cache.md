# Tạo Service với Static Cache
```php
namespace App\Services;

use Corcel\Model\Taxonomy;

class WordpressService
{
    // Biến static này đóng vai trò như một "bộ nhớ tạm" trong 1 Request
    protected static $categories = null;

    public function getCategories()
    {
        // Nếu biến này đã có dữ liệu, trả về ngay lập tức (0 query SQL)
        if (static::$categories !== null) {
            return static::$categories;
        }

        // Nếu chưa có (lần đầu gọi), thực hiện truy vấn và lưu lại
        static::$categories = Taxonomy::where('taxonomy', 'category')
            ->where('count', '>', 0)
            ->with('term')
            ->get();

        return static::$categories;
    }
}
```
## Sử dụng ở nhiều nơi
```md
Bước 2: Sử dụng ở nhiều nơi Dù bạn gọi ở bất cứ đâu trong cùng một lần tải trang, SQL chỉ chạy đúng 1 lần:

Trong Header Component: $service->getCategories(); → Query SQL chạy.

Trong Sidebar Component: $service->getCategories(); → Lấy từ biến static (0 Query).

Trong Footer Component: $service->getCategories(); → Lấy từ biến static (0 Query).
```
## Code kết hợp Static + Redis
```php
namespace App\Services;

use Corcel\Model\Taxonomy;
use Illuminate\Support\Facades\Cache;

class WordpressService
{
    // Lớp 1: RAM PHP (chỉ sống trong 1 Request)
    protected static $categories = null;

    public function getCategories()
    {
        // Kiểm tra Lớp 1: Nếu đã có trong RAM PHP thì trả về luôn (Tốc độ nhanh nhất)
        if (static::$categories !== null) {
            return static::$categories;
        }

        // Kiểm tra Lớp 2: Tìm trong Redis (Tồn tại qua nhiều lần load trang)
        // Nếu không có trong Redis, nó sẽ chạy hàm Closure để lấy từ SQL
        static::$categories = Cache::remember('wp_categories_list', 3600, function () {
            // Lớp 3: SQL (Chỉ chạy khi 2 lớp trên đều trống)
            return Taxonomy::where('taxonomy', 'category')
                ->where('count', '>', 0)
                ->with('term')
                ->get();
        });

        return static::$categories;
    }
}
```

## Cách làm "Xịn" hơn: Base Service
```php
public function getFromCache($key, $callback, $ttl = 3600)
{
    if (!isset(static::$internalCache[$key])) {
        static::$internalCache[$key] = Cache::remember($key, $ttl, $callback);
    }
    return static::$internalCache[$key];
}
```