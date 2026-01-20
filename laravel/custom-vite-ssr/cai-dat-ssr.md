# cai dat vite dev ssr css js
# loại bỏ .vscode/settings.json
```settings.json
{
  "css.lint.unknownAtRules": "ignore"
}
```
## package.json
```package.json
"scripts": {
        "dev-vite-ssr": "cd vite-ssr && vite",
        "build": "vite build",
        "dev": "npm run dev-vite-ssr"
    },
```
## vite.config.js
```vite.config.js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';
// import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
    plugins: [
        laravel({
            input: ["scss/main.scss", "js/main.js"],
            refresh: true,
            // Ép Vite tạo file 'hot' ở thư mục public gốc của Laravel
            hotFile: "../public/hot",
            buildDirectory: "vite-ssr-dist",
        }),
    ],
    // Quan trọng: Chỉ định thư mục public của Laravel gốc
    publicDir: "../public",
    server: {
        watch: {
            ignored: ["**/storage/framework/views/**"],
        },
    },
});

```
### giai thich cau hinh
#### input: ["scss/main.scss", "js/main.js"]
```md
input: ["scss/main.scss", "js/main.js"]:

Đây là các "điểm đầu vào" (entry points) của ứng dụng. Vite sẽ theo dõi các file này để biên dịch.

Vì file cấu hình nằm trong thư mục vite-ssr, nên Vite sẽ tìm các file này theo đường dẫn ./vite-ssr/scss/main.scss.
```

### hotFile: "../public/hot"
```md
hotFile: "../public/hot":

Đây là dòng quan trọng nhất trong trường hợp của bạn. Khi bạn chạy npm run dev, Vite tạo ra một file tên là hot. File này chứa URL của server Vite (ví dụ: http://localhost:5173).

Mặc định, Laravel tìm file này tại public/hot. Vì bạn đang ở trong thư mục con, bạn phải dùng ../ để đẩy file này ra thư mục public gốc của Laravel. Nếu không có dòng này, Laravel sẽ không biết Vite đang chạy ở đâu để kết nối.
```
### buildDirectory: "vite-ssr-dist"
```md
buildDirectory: "vite-ssr-dist":

Cấu hình này xác định tên thư mục chứa các file đã biên dịch xong (khi chạy npm run build).

Thay vì dùng thư mục mặc định là build, bạn đổi tên thành vite-ssr-dist để tránh xung đột với các cấu hình Vite khác nếu có.
```
### publicDir: "../public"
```md
publicDir: "../public":

Mặc định, Vite sẽ copy các file tĩnh từ thư mục public cục bộ. Bằng cách trỏ ra ngoài ../public, bạn cho phép Vite truy cập và phục vụ các file tĩnh (như hình ảnh, font) từ thư mục public chính của Laravel.
```

### server: { watch: { ignored: [...] } }
```md
server: { watch: { ignored: [...] } }:

Yêu cầu Vite không theo dõi các file trong thư mục cache của Laravel (storage/framework/views). Điều này giúp tăng hiệu năng và tránh việc Vite khởi động lại vô ích khi Laravel tự tạo file cache.
```
## Giải thích Directive @vite(...) trong Blade
```blade
@vite(['scss/main.scss', 'js/main.js'], 'vite-ssr-dist')
```

```md
Giải thích Directive @vite(...) trong Blade
Blade

@vite(['scss/main.scss', 'js/main.js'], 'vite-ssr-dist')
Hàm này trong Laravel thực hiện 2 nhiệm vụ tùy theo môi trường:

Môi trường Development (npm run dev):

Laravel sẽ kiểm tra file public/hot.

Nó sẽ tự động tạo ra các thẻ <script type="module"> và <link rel="stylesheet"> trỏ trực tiếp đến server Vite (ví dụ: http://localhost:5173/scss/main.scss).

Tại sao không dùng .css? Vì ở chế độ dev, Vite biên dịch trực tiếp từ .scss trong bộ nhớ, nên bạn phải trỏ vào file nguồn .scss.

Môi trường Production (npm run build):

Laravel sẽ tìm file manifest.json trong thư mục public/vite-ssr-dist/.

Tham số thứ hai 'vite-ssr-dist' chính là để báo cho Laravel biết: "Đừng tìm trong thư mục build mặc định, hãy tìm trong vite-ssr-dist".

Nó sẽ đọc manifest để lấy tên file đã được mã hóa (ví dụ: main-A7b2.css) và chèn vào HTML.
```