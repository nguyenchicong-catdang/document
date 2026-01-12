# Phân tích về Hiệu suất (Performance)
```md
1. Phân tích về Hiệu suất (Performance)
Classic Editor:

Dữ liệu: Lưu trong Database là một chuỗi HTML thuần (<p>, <h2>, <img>).

Laravel xử lý: Cực nhanh. Bạn chỉ cần {!! $post->post_content !!} là xong. Không tốn tài nguyên xử lý logic.

Block Editor (Gutenberg):

Dữ liệu: Lưu trong Database là HTML kèm theo các "mã block" (ví dụ: ...).

Laravel xử lý: Nếu bạn chỉ in ra, nó vẫn chạy, nhưng để render "chuẩn" (ví dụ block Column, Gallery), bạn cần xử lý thêm CSS hoặc parse chuỗi.
```