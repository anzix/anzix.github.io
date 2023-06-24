+++
title = "Greenclip - Минималистичный Rofi/dmenu clipboard менеджер с поддержкой картинок и исключениями"
date = 2023-03-23T22:22:51+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- https://github.com/erebe/greenclip

Для тех кому не нравится GUI менеджеры буфера обмена

Качаем [AUR пакет](https://aur.archlinux.org/packages/rofi-greenclip)

```sh
yay -S rofi-greenclip
```

Создаём конфиг

```sh
cat << EOF > ~/.config/greenclip.toml
  [greenclip]
  history_file = "/home/$USER/.cache/greenclip.history"
  max_history_length = 50
  max_selection_size_bytes = 0
  trim_space_from_selection = true
  use_primary_selection_as_input = false
  blacklisted_applications = []
  enable_image_support = true
  image_cache_directory = "/tmp/greenclip"
  static_history = [
 '''¯\_(ツ)_/¯''',
]
EOF
```

Прописываем его в .xprofile (для DM) или .xinitrc (запуск с TTY) для автозапуска

```sh
greenclip daemon &
```

Или используя systemd сервис

```sh
systemctl --user enable greenclip.service
```

Бинды для sxhkd

```sh
# Greenclip clipboard manager [Rofi]
super + v
    rofi -modi "clipboard:greenclip print" -show clipboard -run-command '{cmd}'
```

> Примечание: У меня почему-то dmenu бинд одной командой не выполняется, решил проблему создав bash скрипт, сделав его исполняемым и всё заработало.

```sh
cat << EOF > ~/.local/bin/dmenu-greenclip
#!/bin/bash
greenclip print | grep . | dmenu -i -l 10 -p clipboard | xargs -r -d'\n' -I '{}' greenclip print '{}'
EOF
```

После чего назначив в sxhkd, dmenu вариант заработал без проблем

```
# Greenclip clipboard manager [dmenu]
super + v
    dmenu-greenclip
```

## Игнор буфера из приложений

К примеру мне не нужно чтобы менеджер буфера отслеживал мои пароли из keepassxc

Запускаем демон

```sh
greenclip
```

Затем копируем что-либо из keepassxc. Копируем в output'е с кавычками "Qt Selection Owner for keepassxc"

И вставляем в конфиг ~/.config/greenclip.toml и сохраняем

```sh
blacklisted_applications = ["Qt Selection Owner for keepassxc"]
```

После чего менеджер не будет копировать любой output из keepassxc
