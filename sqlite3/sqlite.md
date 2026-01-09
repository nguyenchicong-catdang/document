# sqlite3 database/database.sqlite
> sqlite3 /home/cong/git/learn-laravel/database/seeders/DatabaseSeeder.php
# Liệt kê tất cả các bảng đang có (users, sessions,...)
> .tables
# xem cau truc table
> .schema users
# xem bang
> SELECT * FROM users;
# thoat
> .exit
# Cách bật chế độ bảng (Table Mode)
```sql
.mode table
.headers on
```