# redis nhất quán dữ liệu (Data Consistency)
## Sử dụng Webhook (Khuyên dùng)
```php
add_action('save_post', 'clear_laravel_cache_on_save', 10, 3);

function clear_laravel_cache_on_save($post_id, $post, $update) {
    // Không chạy nếu là bản nháp (autosave)
    if (defined('DOING_AUTOSAVE') && DOING_AUTOSAVE) return;

    // Gửi request sang Laravel
    wp_remote_post('https://website.com/api/webhook/clear-cache', [
        'body' => [
            'post_id' => $post_id,
            'slug'    => $post->post_name,
            'secret'  => 'your-secret-key' // Bảo mật để tránh người lạ gọi bừa
        ]
    ]);
}
```

## Phía Laravel (Route & Controller)

```php
// routes/api.php
Route::post('/webhook/clear-cache', function (Request $request) {
    // Kiểm tra secret key
    if ($request->secret !== config('services.wp_webhook_secret')) {
        return response()->json(['message' => 'Unauthorized'], 401);
    }

    $slug = $request->slug;
    
    // Xóa cache của bài viết đó
    Cache::forget("post_{$slug}");
    
    // Nếu bạn có cache danh sách trang chủ, xóa luôn
    Cache::forget("home_posts");

    return response()->json(['success' => true]);
});
```

# Sử dụng Cache Tags (Nếu dùng Redis/Memcached)
```php
$post = Cache::tags(['posts'])->remember("post_{$slug}", 3600, function () use ($slug) {
    return Post::slug($slug)->first();
});
```
## Khi nhận Webhook từ WordPress:
```php
// Xóa toàn bộ cache có tag là 'posts'
Cache::tags(['posts'])->flush();
```
## Kiểm tra updated_at (Không cần Webhook)
```php
// 1. Lấy thời gian cập nhật mới nhất (Truy vấn này cực nhanh vì chỉ lấy 1 cột)
$updatedAt = DB::connection('wordpress')
               ->table('posts')
               ->where('post_name', $slug)
               ->value('post_modified');

// 2. Dùng thời gian này làm một phần của Key cache
$post = Cache::remember("post_{$slug}_{$updatedAt}", 3600, function () use ($slug) {
    return Post::slug($slug)->first();
});
```