> laravel new laravel-app

> composer update

> composer show ncc/*

> composer dump-autoload

"settings": {
    // Đoạn này giúp ẩn thư mục ở cột Explorer nhưng Git vẫn quét thấy
    // Thêm dòng này để ép Git phải quét cả thư mục đã xóa ở trên
    "git.scanRepositories": ["../laravel-wp-local-packages"],
  },

> https://github.com/WordPress/theme-test-data/blob/master/themeunittestdata.wordpress.xml

# Tạo 50 bài viết mẫu
wp post generate --count=50 --post_content --post_date=2026-03-30
