+++
title = "Японский текст в wine"
date = 2023-02-23T20:30:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Можно использовать на выбор две переменные для смены на Японский

```sh
LC_ALL="ja_JP.UTF-8"
# или
LC_ALL="ja_JP.Shift_JIS"
```

НО японский появился только на 10%, весь остальной текст в чёрных прямоугольниках\
Исправить это можно двумя способами:

1. Если у вас есть копия шрифтов Windows 10 их можно перенести в данный каталог ``/usr/share/fonts/WindowsFonts``

```sh
sudo mkdir /usr/share/fonts/WindowsFonts
sudo cp /windows/Windows/Fonts/* /usr/share/fonts/WindowsFonts/
sudo chmod 644 /usr/share/fonts/WindowsFonts/*
```

2. Или скачать AUR пакет [ttf-ms-win11-auto](https://aur.archlinux.org/packages/ttf-ms-win11-auto)

И после установки шрифтов выполнив команду

```sh
sudo fc-cache --force
```

После чего язык будет отображаться на 100%

```sh
LC_ALL="ja_JP.UTF-8" WINEPREFIX="$HOME/.local/share/wineprefixes/TH6" wine install.exe
```

Проверено на японском установщике Touhou 6 The Embodiment of Scarlet Devil

![image](/images/japanese-text-in-wine/Screenshot_20230204_032901.png)
