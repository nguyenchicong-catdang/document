# .bash_aliases

> nano ~/.bash_aliases

## function run-adminer
dirLaravelWp=~/git/laravel-wp/wp-app
run-wp() {
     cd "$dirLaravelWp"
     wp server
}