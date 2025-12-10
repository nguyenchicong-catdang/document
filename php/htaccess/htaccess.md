# Bật module RewriteEngine
RewriteEngine On

# Đảm bảo rằng tệp .htaccess này nằm trong thư mục gốc (hoặc thư mục /api/)
# Nếu nó nằm trong thư mục gốc, hãy bỏ dòng này:
# RewriteBase /

# 1. Điều kiện: Chỉ áp dụng nếu đường dẫn KHÔNG phải là một tệp hoặc thư mục có thật.
# Điều này tránh việc ghi đè lên các tệp CSS/JS/ảnh/thư mục có thật
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# 2. Quy tắc Rewrite:
# Nếu yêu cầu bắt đầu bằng /api/ (Regex: ^api/)
# Và có bất kỳ ký tự nào theo sau (.*)
RewriteRule ^api/(.*)$ api/index.php [L,QSA]
#                 HOẶC nếu bạn muốn ẩn .php
# RewriteRule ^api/(.*)$ api/index [L,QSA]