# cai dat tai thu muc goc du an

wp core download --path=wp-app

.gitignore wp-app/

cd wp-app && wp config create --dbhost=127.0.0.1 --dbname=laravel_wp --dbuser=laravel_wp --prompt=dbpass

wp db create

wp core install --url=127.0.0.1:8080 --title="LARAVEL WP" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org

wp core install --url=127.0.0.1:8080 --title="LARAVEL WP" --admin_user=admin --admin_password=cong12345 --admin_email=info@wp-cli.org

wp option update upload_path uploads

wp option update upload_url_path /uploads

wp config set WP_POST_REVISIONS 1 --raw

wp server