> nano ~/.bash_aliases

# ~/.bash_aliases

run-laravel() {
    cd ~/git/laravel-wp/laravel-app
    echo "--- Dang test ---"
}

# Bạn có thể thêm các function khác vào đây sau này

> nano ~/.bashrc

# Neu file .bash_aliases ton tai, hay nap no
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi

> source ~/.bashrc