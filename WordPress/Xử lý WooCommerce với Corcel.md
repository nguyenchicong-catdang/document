# Xử lý WooCommerce với Corcel
> composer require corcel/woocommerce

# Truy vấn Product: Sản phẩm trong WP thực chất là một Post với post_type là product.

```php
use Corcel\WooCommerce\Model\Product;

// Lấy sản phẩm và giá (Price lưu trong Meta)
$product = Product::published()->where('post_name', $slug)->first();
$price = $product->meta->_price;
$stock = $product->meta->_stock_status; // 'instock' or 'outofstock'
```

## Xử lý Yoast SEO và Schema

### Cách A: Thủ công (Lấy từng trường Meta)
```php
// Trong Controller Laravel
$data['seo'] = [
    'title' => $post->meta->_yoast_wpseo_title ?: $post->post_title,
    'description' => $post->meta->_yoast_wpseo_metadesc,
    'canonical' => $post->meta->_yoast_wpseo_canonical,
];
```

```html
<title>{{ $seo['title'] }}</title>
<meta name="description" content="{{ $seo['description'] }}">
```

## Dùng thư viện hỗ trợ Yoast (Khuyên dùng)

> Có một số package như corcel/yoast

## Xử lý Gutenberg Blocks (SSR với Blade)

```php
<article class="prose">
    {!! $post->post_content !!}
</article>
```

## Xử lý Schema.org (Cấu trúc dữ liệu)
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org/",
  "@type": "Product",
  "name": "{{ $product->post_title }}",
  "image": "{{ $product->thumbnail }}",
  "description": "{{ $product->meta->_yoast_wpseo_metadesc }}",
  "offers": {
    "@type": "Offer",
    "price": "{{ $product->meta->_price }}",
    "priceCurrency": "VND"
  }
}
</script>
```