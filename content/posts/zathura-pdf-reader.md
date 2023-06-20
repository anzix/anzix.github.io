+++
title = "Zathura - простой PDF читатель"
date = 2023-03-08T19:19:57+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

- https://wiki.archlinux.org/title/Zathura

Качаем данные пакеты (zathura-cb позволяет просматривать через zathura архивированные .cbz аниме мангу, хентай и т.д)

```sh
sudo pacman -S zathura-pdf-mupdf  zathura-djvu zathura-cb
```

## Простая конфигурация

Создаём конфиг файл

```sh
mkdir ~/.config/zathura/
nvim ~/.config/zathura/zathurarc
```

С данным содержимым внутри

```sh
# Мануал: man zathurarc

# --- Основное ---

# Шрифт
set font Hack normal 9

# Use primary selection instead of clipboard for copying
set selection-clipboard clipboard

# Показать: statusline, horizontal scrollbar, vertical scrollbar
# set guioptions shv

# Show ~ instead of home/ on the title
set window-title-home-tilde true

# Show ~ instead of home/ on status bar
set statusbar-home-tilde true

# Show page number dynamically in the title too, eg [3/7]
set window-title-page true

# Fit on open
set adjust-open best-fit

# Инкрементальный поиск при вводе
set incremental-search true

# Сохранение исходного оттенка при инвертировании цветов
set recolor-keephue

# Инвертировать цвета
set recolor

# Цвета
set inputbar-bg \#404552
set statusbar-bg \#404552

# --- Custom mappings ---

# Invert colors
map I set recolor

# Toggle status bar
map t toggle_statusbar

# vim: ft=conf
```

## !Фикс медленный скролл мыши используя imwheel

Для себя я выставил значения 2.9

Правим `.imwheelrc`

```sh
"^org.pwmt.zathura$"
	None, Up, Button4, 2.9
	None, Down, Button5, 2.9
```
