+++
title = "Иконки контекстного меню не отображаются GTK2/3"
date = 2023-03-24T14:37:28+05:00
draft = false
[taxonomies]
categories = ["solving-problem"]
tags = ["linux"]
+++
Источник решения:
* [Arch Wiki](https://wiki.archlinux.org/title/GTK#Button_and_menu_icons)

Это довольно просто решается

Открываем конфиги для gtk2 находящийся в `~/.gtkrc-2.0` или `~/.config/gtk-2.0/gtkrc-2.0`

Для gtk3 `~/.config/gtk-3.0/settings.ini`

И редактируем эти файлы меняя только значения этих строк с `0` на `1`
```sh
gtk-button-images=1
gtk-menu-images=1
```
Перезапускаем pcmanfm и проблема решена

