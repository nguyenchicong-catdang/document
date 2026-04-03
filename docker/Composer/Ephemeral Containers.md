# Ephemeral Containers (Container tạm thời)

> docker run --rm -v .:/app -w /app composer install

--rm: Đây là phím tắt của "Remove". Bình thường khi bạn chạy một container, dù nó chạy xong và dừng lại, nó vẫn nằm lỳ trong ổ cứng (trạng thái Exited).

Tác dụng: Thêm --rm vào, ngay khi Composer chạy xong dòng cuối cùng, Docker sẽ xóa sổ cái container đó ngay lập tức. Nó "rút" đi không để lại dấu vết gì ngoài thư mục vendor đã được tải về máy bạn.

"Chui vào bụng" (-v .:/app)
-v .:/app (Volume): Đây là cách bạn mang toàn bộ code Laravel từ máy thật "nhét" vào bụng container tại đường dẫn /app.

Cơ chế: Container Composer thực chất là một "cỗ máy rỗng" có sẵn PHP và Composer. Khi bạn nhét code vào bụng nó, nó nhìn thấy file composer.json và bắt đầu làm việc.

Fix lỗi Permission: Khi dùng -v, file vendor

# Cài mới toàn bộ vendor
docker run --rm -v $(pwd):/app -w /app composer install

# Hoặc cài thêm một package (ví dụ Laravel Sanctum)
docker run --rm -v $(pwd):/app -w /app composer require laravel/sanctum

docker run --rm -v $(pwd):/app -w /app composer install

--rm: Xóa cái "xác" container ngay khi chạy xong.

-v $(pwd):/app: Mang code Laravel "nhét" vào bụng container.

-w /app: Chỉ định "phòng làm việc" bên trong container.

docker run --rm -u $(id -u):$(id -g) -v $(pwd):/app -w /app composer install

docker run --rm -it \
  -u $(id -u):$(id -g) \
  -v $(pwd):/app \
  -w /app \
  composer install

  -it: Để bạn thấy được thanh quá trình (progress bar) đang chạy cho sướng mắt.

$(pwd): Đảm bảo đường dẫn luôn đúng dù bạn đang đứng ở folder nào trong WSL2.