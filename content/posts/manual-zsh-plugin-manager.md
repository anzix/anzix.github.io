+++
title = "!Минималистичный менеджер плагинов zsh своими руками"
date = 2023-03-29T22:57:48+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
* Вопрос: Есть oh-my-zsh, чем он не устраивает?
* Ответ: Потому-что всё то что делает omz можно добится 4-мя функциями. И это без навязывания обновлений и рекламой их мерча.

Создаём zsh конфиг файл и вставляем данные функции
```sh
nvim functions.zsh
```

```sh
# Функция чтобы выполнять содержимое файла
# в текущей оболочке, если они существуют
function file() {
    [ -f "$ZDOTDIR/$1" ] && source "$ZDOTDIR/$1"
}

# Функция для выполнения sparse clone вытягивания плагинов oh-my-zsh
# https://stackoverflow.com/a/13738951
function git_omz_plugins() (
  rurl="$1" tmpdir="$ZDOTDIR/tmp" && shift "$4" # Для предотвращения "shift:1: shift count must be <= $#"

  mkdir -p "$tmpdir"
  cd "$tmpdir"

  git init > /dev/null 2>&1
  git remote add -f origin "$rurl" > /dev/null 2>&1
  git config core.sparseCheckout true

  # Loops over remaining args
  for i; do
    echo "$i" >> .git/info/sparse-checkout
  done
  git pull origin master > /dev/null 2>&1

  echo "Удаляю .git"
  rm -rf .git/
  echo "Перемещаю плагин в $ZDOTDIR/plugins/"
  mv plugins/* $ZDOTDIR/plugins/
  echo "Удаляю $tmpdir"
  rm -rf $tmpdir
  echo "Функция git_omz_plugins завершена"
)

# Вытягивает плагин из репо omz
# https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins
function omz_plug() {
    PLUGIN_NAME=$(echo $1 )
    if [ -d "$ZDOTDIR/plugins/$PLUGIN_NAME" ]; then
        # For plugins
        file "plugins/$PLUGIN_NAME/$PLUGIN_NAME.plugin.zsh" || \
        file "plugins/$PLUGIN_NAME/$PLUGIN_NAME.zsh"
    else

        if read -q "choice?Установить плагин $PLUGIN_NAME ?: "; then
            echo
            echo "Добавляю плагин $PLUGIN_NAME"
            git_omz_plugins "http://github.com/ohmyzsh/ohmyzsh/" "plugins/$PLUGIN_NAME"
        else
            echo
            echo "Пропускаю..."
        fi
    fi
}

# Вытягивает плагин из репо unixorn
# https://github.com/unixorn/awesome-zsh-plugins#plugins
function plug() {
    PLUGIN_NAME=$(echo $1 | cut -d "/" -f 2)
    if [ -d "$ZDOTDIR/plugins/$PLUGIN_NAME" ]; then
        # For plugins
        file "plugins/$PLUGIN_NAME/$PLUGIN_NAME.plugin.zsh" || \
        file "plugins/$PLUGIN_NAME/$PLUGIN_NAME.zsh"
    else
        git clone "https://github.com/$1.git" "$ZDOTDIR/plugins/$PLUGIN_NAME"
    fi
}
```
Вставляем в `.zprofile`

```sh
export XDG_CONFIG_HOME="$HOME/.config"
export ZDOTDIR="${XDG_CONFIG_HOME:-$HOME/.config}/zsh" # Папка zsh
export ZPLUGDIR="$ZDOTDIR/plugins" # Папка zsh плагинов
```

Вставляем в `.zshrc`
```sh
source "$ZDOTDIR/functions.zsh"

# Плагины (Автозагрузка, если отсутствуют)
# (plug) Для большего выбора плагинов: https://github.com/unixorn/awesome-zsh-plugins#plugins
plug "zsh-users/zsh-autosuggestions" # Автозаполнение
plug "zsh-users/zsh-syntax-highlighting" # Подсветка синтаксиса
plug "zsh-users/zsh-history-substring-search"
plug "MichaelAquilina/zsh-you-should-use" # Напоминалка о использовании aliases
plug "MichaelAquilina/zsh-auto-notify" # Уведомления shell
plug "hlissner/zsh-autopair" # Полезно для работы с кавычками
plug "romkatv/powerlevel10k" # Prompt
plug "junegunn/fzf" # Fuzzy finder (fzf_install функция находится в functions.zsh)
# plug "Aloxaf/fzf-tab" # TAB fzf функционал

# (omz_plug) Плагины из репо oh-my-zsh: https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins
omz_plug "command-not-found" ; [[ ! -f /bin/pkgfile ]] && sudo pacman -S pkgfile --noconfirm && sudo pkgfile -u # Находит пакет если команда не найдена
omz_plug "dirhistory" # Быстрое перемещение по каталогам зажать alt+[стрелки]

source $ZPLUGDIR/zsh-you-should-use/you-should-use.plugin.zsh
source $ZPLUGDIR/zsh-auto-notify/auto-notify.plugin.zsh
source $ZPLUGDIR/powerlevel10k/powerlevel10k.zsh-theme
```
