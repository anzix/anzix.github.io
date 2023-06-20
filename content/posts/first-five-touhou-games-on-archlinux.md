+++
title = "Touhou 1-5 (Эмулятор PC-98) на Arch Linux"
date = 2023-03-15T00:06:12+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++

Источники:
* [(Wiki) Запуск Touhou в Linux RU](https://ru.touhouwiki.net/wiki/%D0%97%D0%B0%D0%BF%D1%83%D1%81%D0%BA_Touhou_%D0%B2_Linux)
* [(Wiki) Running in Linux and macOS ENG](https://en.touhouwiki.net/wiki/Running_in_Linux_and_macOS)
* [Running Touhou in Linux](https://touhou.fandom.com/wiki/Running_in_Linux)
* [Arch Wiki Touhou](https://wiki.archlinux.org/title/Touhou)

Скачиваем AUR пакет [xnp2](https://aur.archlinux.org/packages/xnp2)
```sh
yay -S xnp2
```
> **Важно**: **<span style="color:red">не запускайте</span>** `xnp2` (без 1 в конце) иначе не можете запустить ни одну Touhou игру (будет вылезать красный текст с надписью Stop). Отличие в том что `xnp21` активирует IA-32 необходимый как раз для запуска Touhou игр

Запуск производится через терминал командой:
```sh
xnp21
```

Шрифты:

[Скачиваем шрифт](https://mega.nz/#!mBwgDAxB!AvMsrNGlR46IqU9O8yYMuYWAaGUOlcuM3YXP-FgvRyw) от эмуля anex86 и закидываем ~/.np2/ переименовывая его в font.tmp с заменой

Настройка:

В тулбаре **Emulate** - **Configure..**
1. CPU выставляем x 4 на 16 (Ускоряет запуск эмулятора)
2. Sound в Buffer выставляем 20ms (Делает задержку звука минимальной)

В тулбаре **Device** - **Memory** выставляем 13.6MB (Больше памяти лучше геймплей)

Запуск:

В тулбаре **HardDisk** - **IDE0-0** - **Open..** и выбираем образ игры Touhou. После выбора в тулбаре **Emulate** - **Reset** перезагружаем эмулятор для загрузки образа

Наслаждайтесь!

