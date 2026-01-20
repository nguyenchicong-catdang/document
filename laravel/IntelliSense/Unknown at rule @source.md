# Unknown at rule @source
Cách xử lý bằng file (nếu bạn muốn tắt cho riêng project này): Tạo hoặc mở file .vscode/settings.json trong thư mục gốc của project và dán nội dung sau:

```.vscode/settings.json
{
  "css.lint.unknownAtRules": "ignore"
}
```