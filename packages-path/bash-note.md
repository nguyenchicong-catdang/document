# note
CURRENT_BRANCH=$(cd "$ROOT_BASH" && git rev-parse --abbrev-ref HEAD)
echo "Đang ở nhánh: $CURRENT_BRANCH. Tự động cấu hình Composer..."

SCRIPT_DIR=$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" &> /dev/null && pwd)

sed -i "s#dev-\*#dev-$CURRENT_BRANCH#g" "$DEST_FILE"

> export ROOT_WP_PLUGIN=$(cd -- "$SCRIPT_DIR/.." && pwd)


> composer update vendor-path/packages-src --prefer-source

cd "$RUN_LARAVEL" || exit
    # rm -f composer.lock
    # composer install
    # composer dump-autoload

