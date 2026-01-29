## branch
> CURRENT_BRANCH=$(cd "$ROOT_BASH" && git rev-parse --abbrev-ref HEAD)

## xử lý chuỗi
> sed -i "s#dev-\*#dev-$CURRENT_BRANCH#g" "$DEST_FILE"
# 4. Chạy Composer
```bash
(
    cd "$RUN_LARAVEL" || exit
    rm -f composer.lock
    composer install
)
```
## pwd bash
> SCRIPT_DIR=$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" &> /dev/null && pwd)

> export ROOT_BASH=$(cd -- "$SCRIPT_DIR/.." && pwd)

> bash "$ROOT_BASH/setup/bash/copy_laravel-app_composer-json.sh"

. bash.sh
