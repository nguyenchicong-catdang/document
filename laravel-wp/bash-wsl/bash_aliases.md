# Thêm dòng này vào ~/.bash_aliases
reload-bash() {
        source ~/.bashrc
        echo "--- Da cap nhat code moi cho Bash! ---"
}
# Khai bao biến
dirLaravel=~/git/laravel-wp/laravel-app
dirLaravel=~/git/laravel-wp/laravel-app
# Run laravel serve
run-laravel() {
        cd "$dirLaravel"
        php artisan serve
}
# Cd laravel
cd-laravel() {
        cd "$dirLaravel"
}

# nano ~/.bashrc

nano ~/.bashrc
# Neu file .bash_aliases ton tai, hay nap no
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

source ~/.bashrc
