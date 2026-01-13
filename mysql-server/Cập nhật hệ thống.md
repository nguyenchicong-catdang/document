# Cập nhật hệ thống
> sudo apt update && sudo apt upgrade -y

> sudo apt install mysql-server -y

> sudo service mysql start

> sudo mysql_secure_installation

# Cấp quyền sở hữu cho user mysql
sudo chown -R mysql:mysql /var/run/mysqld

# Cấp quyền đọc/ghi cho thư mục socket
sudo chmod -R 777 /var/run/mysqld