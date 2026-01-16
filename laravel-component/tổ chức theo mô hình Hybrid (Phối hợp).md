# tổ chức theo mô hình Hybrid (Phối hợp)
## Cấu trúc thư mục đề xuất
```md
src/
└── Components/
    ├── Header.php           (Class xử lý logic lấy dữ liệu từ Model)
    ├── Header/              (Thư mục chứa các component con)
    │   ├── logo.blade.php   (Anonymous - chỉ giao diện)
    │   ├── nav.blade.php    (Anonymous - chỉ giao diện)
    │   └── user-bar.blade.php
    └── views/
        └── header.blade.php (View chính của Header class)
```

## Cách đăng ký trong Service Provider
```php
public function boot(): void
{
    // 1. Đăng ký cho Class-based (Header.php)
    // Giả sử Namespace của bạn là: MyPackage\Components
    Blade::componentNamespace('MyPackage\\Components', 'layout');

    // 2. Đăng ký cho các component con (Anonymous)
    // Việc này giúp bạn gọi các file trong thư mục Header/ dễ dàng
    Blade::anonymousComponentPath(__DIR__.'/../src/Components/Header', 'header');
}
```

## Triển khai Code
```php
namespace MyPackage\Components;

use Illuminate\View\Component;
use App\Models\Menu; // Ví dụ lấy dữ liệu từ Model

class Header extends Component
{
    public $menuItems;

    public function __construct()
    {
        // Lấy dữ liệu từ Model tại đây
        $this->menuItems = Menu::all();
    }

    public function render()
    {
        // Trỏ đến file view chính
        return view('nightshade::header'); 
    }
}
```

## File View chính: src/Components/views/header.blade.php
```blade.php
<header class="main-header">
    <x-header::logo :brand="$brandName" />
    
    <nav>
        <x-header::nav :items="$menuItems" />
    </nav>

    <x-header::user-bar />
</header>
```

## Cách sử dụng ngoài View (End-user)

> <x-layout::header />