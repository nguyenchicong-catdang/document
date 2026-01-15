# Run and Debug
## Cài đặt Extension trên VS Code
> PHP Debug
## Cài đặt và cấu hình Xdebug trên Ubuntu (WSL)
> sudo apt install php8.3-xdebug
## Cấu hình: Bạn cần tìm file xdebug.ini (thường nằm ở /etc/php/8.x/mods-available/xdebug.ini) và thêm các dòng sau để bật tính năng gỡ lỗi:

> sudo cat /etc/php/8.3/mods-available/xdebug.ini

> sudo nano /etc/php/8.3/mods-available/xdebug.ini

```Ini, TOML
zend_extension=xdebug.so
xdebug.mode = debug
xdebug.client_host = 127.0.0.1
xdebug.client_port = 9003
xdebug.start_with_request = yes
xdebug.log = /tmp/xdebug.log
```

## Nhấn vào biểu tượng Run and Debug -> create a launch.json file -> chọn PHP.
