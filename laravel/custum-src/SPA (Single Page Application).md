# SPA (Single Page Application)
## Cấu trúc index.blade.php (The Shell)
```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Professional SPA</title>
    @vite(['resources/css/app.css', 'resources/js/app.js'])
</head>
<body>
    
    <div id="root"></div>

    <script>
        // Truyền một số biến toàn cục từ Laravel sang JS nếu cần
        window.Laravel = {
            csrfToken: '{{ csrf_token() }}',
            user: @json(auth()->user()), // Trả về user nếu đã login qua Blade
        };
    </script>
</body>
</html>
```
## Kiểm tra trạng thái User ngay khi load trang (SPA Auth Check)
```bash
// resources/js/app.js
import axiosClient from './axios-client';

async function initApp() {
    try {
        // Gọi API check xem session còn sống không
        // Route này bọc trong middleware('auth:sanctum')
        const user = await axiosClient.get('/user');
        
        console.log("User đã đăng nhập:", user);
        renderApp(user); // Khởi tạo giao diện SPA cho người dùng
    } catch (error) {
        if (error.response && error.response.status === 401) {
            console.log("Chưa đăng nhập hoặc session hết hạn");
            window.location.href = '/login'; // Đẩy về trang login của Blade
        }
    }
}

function renderApp(user) {
    const root = document.getElementById('root');
    root.innerHTML = `<h1>Chào mừng ${user.name} đã quay trở lại!</h1>`;
    // Ở đây bạn khởi tạo React/Vue Router...
}

initApp();
```
## Cách cấu hình Route để biến nó thành SPA thực thụ
```bash
// routes/web.php

// 1. Route login/logout truyền thống
Route::get('/login', [LoginController::class, 'showLoginForm'])->name('login');
Route::post('/login', [LoginController::class, 'login']);
Route::post('/logout', [LoginController::class, 'logout'])->name('logout');

// 2. MỌI route khác đều trả về trang index.blade (SPA quản lý)
// Cần bọc auth để đảm bảo chỉ người dùng đã login mới vào được SPA
Route::middleware('auth')->get('/{any}', function () {
    return view('index');
})->where('any', '.*');
```

## Cấu hình Domain tin tưởng trong .env
### Nếu dùng Vite Proxy:
SANCTUM_STATEFUL_DOMAINS=localhost:5173,127.0.0.1:5173

### Nếu chạy trực tiếp trên cùng domain:
SESSION_DOMAIN=localhost