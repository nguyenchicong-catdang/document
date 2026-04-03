# lỗi radio

<style>
        /* Định nghĩa sơ bộ để giữ chỗ ngay lập tức */
        .ratio {
            position: relative;
            width: 100%;
            display: block;
            background-color: #f0f0f0;
            /* Màu nền nhẹ để người dùng biết có ảnh sắp load */
        }

        .ratio-1x1 {
            padding-top: 100%;
        }

        .ratio>* {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }
    </style>

## tham khảo
.my-figure {
    aspect-ratio: 1 / 1;
    width: 100%;
    background: #f8f9fa; /* Tạo Skeleton giả màu xám nhẹ */
}

# fix => http access
## code php
```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\URL; // Thêm dòng này

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        // Kiểm tra nếu không phải là localhost thì ép sang https
        if (env('APP_ENV') !== 'local') {
            URL::forceScheme('https');
        }
    }
}
```
## fix html
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">

# tham khảo về tải lười

## Tải CSS nặng một cách không đồng bộ (Asynchronous)

Bằng cách thêm media="print" onload="this.media='all'" vào thẻ link CDN:

báo cho trình duyệt chuẩn bị tâm lý kết nối sớm hơn nữa (để giảm thời gian chờ)

<link rel="preconnect" href="https://cdn.jsdelivr.net">

## tham khảo tải
<head>
    <style>
        {!! file_get_contents(public_path('css/critical.css')) !!}
    </style>
    
    <link rel="preload" href="{{ asset('css/app.css') }}" as="style" onload="this.onload=null;this.rel='stylesheet'">
</head>

this.rel='stylesheet' (hoặc đôi khi là all)

location / {
    add_header Link "</css/app.css>; rel=preload; as=style";
}

<link rel="stylesheet" href="..." media="print" onload="this.media='all'">

<noscript><link rel="stylesheet" href="{{ asset('css/app.css') }}"></noscript>

# scss 

// Mixin thông minh
@mixin skeletal-component($name) {
    .#{$name} {
        display: block;
        width: 100%;
        @content; // Chỗ này để nạp các thuộc tính layout riêng biệt
    }
}

// Cách viết code (vẫn tập trung 1 chỗ)
@include skeletal-component('product-card') {
    aspect-ratio: 1/1; 
    margin-bottom: 20px;
}

