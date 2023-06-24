+++
title = "Минималистичный Gif рекордер с выделением (Giph)"
date = 2023-03-23T21:45:25+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Данный инструмент также позволяет записывать в формате WebP и Mp4, но для текущих целей будем юзать как gif рекордер. Можно записывать игровые моменты при помощи опции `-o`

Все опции описаны в `man page giph`

Необходимые пакеты (зависимости):

```sh
sudo pacman -S ffmpeg slop xdotool dmenu rofi
```

Качаем основной [AUR пакет giph](https://aur.archlinux.org/packages/giph)

```sh
yay -S giph
```

Создаём каталог для сохранения гифок

```sh
mkdir ~/Pictures/Gif
```

Биндим используя [sxhkd](https://github.com/baskerville/sxhkd)

```sh
# (dmenu) Начать/Остановить запись GIF рекордера
ctrl + Print
    { giph -f $(echo "Кол-во кадров в секунду" | dmenu) -s -o -y \
    ~/Pictures/Gif/$(date +%s).gif, dunstify "Запись остановлена"; giph --stop }

# (dmenu) Запись с выбором кол-во кадров и продолжительности записи
ctrl + shift + Print
    giph -f $(echo "Кол-во кадров в секунду" | dmenu) -s -o -y \
    -t $(echo "Сколько секунд записать?" | dmenu) \
    ~/Pictures/Gif/$(date +%s).gif

# (rofi) Запись с выбором кол-во кадров и продолжительности записи
ctrl + shift + Print
    giph -f $(echo "Кол-во кадров в секунду" | rofi -dmenu) -s -o -y \
    -t $(echo "Сколько секунд записать?" | rofi -dmenu) \
    ~/Pictures/Gif/$(date +%s).gif
```
