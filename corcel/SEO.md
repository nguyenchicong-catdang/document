# SEO

Để tương tác với dữ liệu SEO (như SEO Title, SEO Description, Schema) từ các plugin phổ biến như Rank Math hay Yoast SEO trong Laravel, bạn cần hiểu rằng các thông tin này thực chất được lưu trữ dưới dạng Post Meta trong bảng wp_postmeta.

# Đối với Rank Math SEO

```md
Meta Keys phổ biến:

SEO Title: rank_math_title

SEO Description: rank_math_description

Focus Keyword: rank_math_focus_keyword

Schema Data: rank_math_schema_post
```

```php
$product = App\Models\WCProduct::find(21);

$seoTitle = $product->getMeta('rank_math_title');
$seoDesc = $product->getMeta('rank_math_description');
// Lấy dữ liệu Schema (thường là dạng JSON)
$schema = $product->getMeta('rank_math_schema_post');
```

# Đối với Yoast SEO
```md
Meta Keys phổ biến:

SEO Title: _yoast_wpseo_title

SEO Description: _yoast_wpseo_metadesc

Focus Keyword: _yoast_wpseo_focuskw
```

```php
$product = App\Models\WCProduct::find(21);

$seoTitle = $product->getMeta('_yoast_wpseo_title');
$seoDesc = $product->getMeta('_yoast_wpseo_metadesc');
```

## Thư viện hỗ trợ chuyên dụng

Gói gợi ý: corcel/acf (nếu bạn dùng ACF kết hợp SEO) hoặc đơn giản nhất là tự định nghĩa một Trait hoặc Accessor trong Model của bạn để dùng chung cho cả dự án.

```php
// Trong app/Models/WCProduct.php

public function getSeoAttribute()
{
    return [
        'title' => $this->getMeta('rank_math_title') ?: $this->post_title,
        'description' => $this->getMeta('rank_math_description') ?: Str::limit(strip_tags($this->content), 160),
        'canonical' => $this->getMeta('rank_math_canonical'),
    ];
}
```

# Cách đưa vào Blade Template
```blade
<title>{{ $product->seo['title'] }}</title>
<meta name="description" content="{{ $product->seo['description'] }}">
```