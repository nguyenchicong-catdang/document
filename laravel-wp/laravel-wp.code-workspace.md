```json
{
  "folders": [
    {
      "name": "DOCUMENT",
      "path": "../document",
    },
    {
      "name": "LARAVEL-WP",
      "path": ".",
    },
    {
      "name": "LARAVEL-WP-PACKAGES",
      "path": "../laravel-wp-local-packages",
    },
  ],
  "settings": {
    // Đoạn này giúp ẩn thư mục ở cột Explorer nhưng Git vẫn quét thấy
    // Thêm dòng này để ép Git phải quét cả thư mục đã xóa ở trên
    "git.scanRepositories": ["../laravel-wp-local-packages"],
  },
}


```