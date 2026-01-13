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

## install
https://make.wordpress.org/cli/handbook/guides/quick-start/

https://make.wordpress.org/cli/handbook/guides/quick-start/#practical-examples

> cd > public

> wp core download

> wp config create --dbname=wp_database --dbuser=wp_user --prompt=dbpass

> wp db create

> wp core install --url=localhost --title="WP-CLI" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org

> wp core install --url=localhost:8000/wp-learn --title="WP-CLI" --admin_user=admin --admin_password=wpcli --admin_email=info@wp-cli.org