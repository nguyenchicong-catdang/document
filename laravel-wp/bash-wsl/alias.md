> cat ~/.bashrc

alias run-adminer='. /home/cong/git/packages-app/laravel-app/vendor/vendor-path/packages-src/run-adminer.sh'
alias run-laravel='. /home/cong/git/packages-app/laravel-app/vendor/vendor-path/packages-src/run-laravel-app.sh'
alias run-wp='. /home/cong/git/packages-app/laravel-app/vendor/vendor-path/packages-src/run-wordpress-app.sh'

alias cd-laravel='cd /home/cong/git/packages-app/laravel-app'

# make alias
> nano ~/.bashrc

> source ~/.bashrc

```bash
run-laravel() {
    cd ~/git/laravel-wp/laravel-app
    echo "--- Dang don dep cache ---"
    php artisan config:clear
    php artisan cache:clear

    echo "--- Bat dau chay project ---"
    # Chay lenh dev cua ban (co ca server, queue, logs, vite)
    composer dev
}
```

# Neu file .bash_aliases ton tai, hay nap no
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi