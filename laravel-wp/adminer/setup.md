> mkdir adminer

> .gitignore adminer/

> cd adminer

> curl -L https://github.com/vrana/adminer/releases/download/v5.4.2/adminer-5.4.2.php -o index.php

# .bash_aliases

> nano ~/.bash_aliases

## function run-adminer
dirAdminer=~/git/laravel-wp/adminer
run-adminer() {
     cd "$dirAdminer"
     php -S localhost:9000
}

# Tạo user moi
CREATE USER 'ten_user'@'localhost' IDENTIFIED BY 'mat_khau_moi';

CREATE USER 'ten_user'@'127.0.0.1' IDENTIFIED BY 'mat_khau_moi';

GRANT ALL PRIVILEGES ON *.* TO 'ten_user'@'localhost';
FLUSH PRIVILEGES;

GRANT ALL PRIVILEGES ON *.* TO 'ten_user'@'127.0.0.1';
FLUSH PRIVILEGES;

## neu quen MK
CREATE USER 'user'@'localhost' IDENTIFIED BY 'mat_khau_moi';