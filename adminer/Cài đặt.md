# Cài đặt
> curl -L https://github.com/vrana/adminer/releases/download/v5.4.1/adminer-5.4.1.php -o index.php

> sudo apt install php8.3-mysql php8.3-mysql php8.3-pdo -y

> sudo service mysql start

> SHOW GRANTS FOR 'root'@'localhost';

```sql
# Vào MySQL
sudo mysql

# Chạy lệnh SQL này
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'mat_khau_cua_ban';
FLUSH PRIVILEGES;
```

> php -S localhost:9000

# create uer

> CREATE USER 'ten_user'@'localhost' IDENTIFIED BY 'mat_khau_moi';

> GRANT ALL PRIVILEGES ON ten_database.* TO 'ten_user'@'localhost';
FLUSH PRIVILEGES;

### quyển root
> GRANT ALL PRIVILEGES ON *.* TO 'ten_user'@'localhost';
FLUSH PRIVILEGES;

> CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'mat_khau_moi';

> GRANT ALL PRIVILEGES ON *.* TO 'ten_user'@'localhost';
> FLUSH PRIVILEGES;