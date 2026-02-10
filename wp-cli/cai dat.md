# cai dat
https://wp-cli.org/

> curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar

> php wp-cli.phar --info

> chmod +x wp-cli.phar

> sudo mv wp-cli.phar /usr/local/bin/wp

> wp --info

# https://developer.wordpress.org/cli/commands/cli/
https://developer.wordpress.org/cli/commands/cli/

> wp cli version

> wp cli check-update

> wp cli update

# install wp-app
> wp core download --path=wp-app

> cd wp-app && wp config create --dbhost=127.0.0.1 --dbname=wp_database --dbuser=wp_user --prompt=dbpass

> wp db create

> wp core install --url=127.0.0.1:8080 --title="WP-CLI" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org

> wp option update upload_path	uploads

> wp option update upload_url_path	/uploads

> wp config set WP_POST_REVISIONS 3 --raw

> wp db optimize

> kiểm tra: wp config get WP_POST_REVISIONS

> wp server

## install
https://make.wordpress.org/cli/handbook/guides/quick-start/

https://make.wordpress.org/cli/handbook/guides/quick-start/#practical-examples

> cd > public

> wp core download

> wp config create --dbname=wp_database --dbuser=wp_user --prompt=dbpass

> wp db create

> wp core install --url=127.0.0.1 --title="WP-CLI" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org

> wp server

## deploay chung mot port
> wp core install --url=localhost:8000/wp-learn --title="WP-CLI" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org

## luu y part
> 49	upload_path	uploads

> 56	upload_url_path	/uploads

> Giới hạn Revisions: Thêm define('WP_POST_REVISIONS', 3); vào wp-config.php.