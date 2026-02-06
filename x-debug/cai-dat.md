# php --ini | grep xdebug
> php --ini | grep xdebug

> nano /etc/php/8.3/cli/conf.d/20-xdebug.ini

```20-xdebug.ini
zend_extension=xdebug.so
xdebug.mode = debug
xdebug.client_host = 127.0.0.1
xdebug.client_port = 9003
xdebug.start_with_request = yes
xdebug.log = /tmp/xdebug.log
```