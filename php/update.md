sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt upgrade -y

php -v

sudo apt install php8.4-gd php8.4-imagick php8.4-mysql php8.4-bcmath

sudo apt install -y php8.4-xml php8.4-dom php8.4-simplexml php8.4-xmlreader php8.4-xmlwriter


cong@DESKTOP-EN5O26C:~/git/laravel-wp/laravel-app$ php -m
[PHP Modules]
calendar
Core
ctype
curl
date
exif
FFI
fileinfo
filter
ftp
gettext
hash
iconv
intl
json
libxml
mbstring
openssl
pcntl
pcre
PDO
Phar
posix
random
readline
Reflection
session
shmop
sockets
sodium
SPL
standard
sysvmsg
sysvsem
sysvshm
tokenizer
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache


sudo phpdismod xdebug

sudo phpenmod xdebug