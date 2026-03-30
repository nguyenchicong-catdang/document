# xdebug.ini

php -i | grep "xdebug.ini"

sudo nano /etc/php/8.3/cli/conf.d/20-xdebug.ini

```ini
zend_extension=xdebug.so
xdebug.mode = debug
xdebug.client_host = 127.0.0.1
xdebug.client_port = 9003
xdebug.start_with_request = yes
xdebug.log = /tmp/xdebug.log
```

# debug

xdebug.start_with_request = trigger

;zend_extension=xdebug.so

xdebug.client_host = host.docker.internal

> cat /etc/resolv.conf | grep nameserver | awk '{print $2}'

> php -m | grep -i xdebug

> php artisan boost:install

> php artisan boost:update --ansi

## fix
cat /etc/resolv.conf | grep nameserver | awk '{print $2}'

sudo nano /etc/php/8.3/cli/conf.d/20-xdebug.ini

xdebug.client_host = 172.24.48.1

xdebug.discover_client_host = yes

sudo service php8.3-fpm restart

## fix v1
;zend_extension=xdebug.so
xdebug.mode = debug
xdebug.client_host = 172.24.48.1
xdebug.discover_client_host = yes
xdebug.client_port = 9003
xdebug.start_with_request = trigger
xdebug.log = /tmp/xdebug.log

zend_extension=xdebug.so
xdebug.mode = debug
xdebug.client_host = 127.0.0.1
xdebug.client_port = 9003
xdebug.start_with_request = trigger
xdebug.log = /tmp/xdebug.log

# Để TẮT hoàn toàn Xdebug

sudo phpdismod xdebug

sudo service php8.3-fpm restart  # Nếu bạn dùng Web

# Để BẬT lại Xdebug (Khi cần debug code):
sudo phpenmod xdebug

sudo service php8.3-fpm restart

## Cách 1: Chèn trực tiếp vào lệnh PHP (Dùng cho Artisan/CLI)
php -dzend_extension=xdebug.so -dxdebug.mode=debug -dxdebug.start_with_request=yes artisan your:command

## Cách 3: Tạo "Phím tắt" (Alias) - Khuyên dùng nhất
alias php-debug='php -dzend_extension=xdebug.so -dxdebug.mode=debug -dxdebug.start_with_request=yes'

php -dzend_extension=xdebug.so -dxdebug.mode=debug -dxdebug.start_with_request=yes artisan serve

php -dzend_extension=xdebug.so -dxdebug.mode=debug -dxdebug.start_with_request=yes -dxdebug.client_host=172.0.0.1

php -dzend_extension=xdebug.so -dxdebug.mode=debug -dxdebug.start_with_request=yes -dxdebug.client_host=127.0.0.1 artisan serve


php -dzend_extension=xdebug.so \
    -dxdebug.mode=debug \
    -dxdebug.start_with_request=yes \
    -dxdebug.client_host=127.0.0.1 \
    -dxdebug.client_port=9003 \
    artisan serve --port=8888