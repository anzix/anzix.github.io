+++
title = "!Mpd + ncmpcpp"
date = 2023-03-22T01:47:28+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

MPD сокращенно (музыкальный демон), а ncmpcpp это музыкальный плеер

Установка и настройка

```sh
sudo pacman -S mpd
mkdir -p ~/.config/mpd/playlists
touch ~/.config/mpd/{database,pid,state}
```

Копируем пример конфига

```sh
cp /usr/share/doc/mpd/mpdconf.example ~/.config/mpd/mpd.conf
nvim ~/.config/mpd/mpd.conf
```

<!-- Либо используем [мой пользовательский конфиг файл]() -->

Запуск демона MPD

1. Прописываем в `.xprofile` (для DM) или `.xinitrc` (запуск с TTY) для автозапуска

```sh
echo "mpd &" >> ~/.xinitrc
```

2. Или запускаем systemd сервис

```sh
systemctl --user enable --now mpd
```

Для проверки запустите status сервиса

```sh
systemctl --user status mpd
```

Также скачиваем mpc, он необходим для уведомления о текущим треке и для обновления базу данных о треке

```sh
sudo pacman -S mpc
```

Для обновления вводим `mpc update`

## ncmpcpp

Это TUI музыкальный клиент

```sh
sudo pacman -S ncmpcpp
mkdir ~/.config/ncmpcpp
```

Копируем пример конфиг файл

```sh
cp /usr/share/doc/ncmpcpp/config ~/.config/ncmpcpp
```

<!-- Либо используем [мой пользовательский конфиг файл]() -->

> ВАЖНО: Для того что понять какая клавиша за что отвечает - нажмите F1

Мини версия клавиш или то что я часто нажимаю на этом плеере

```
# Перемещение по вкладкам с помощью цифр и равно(=)
1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | =

# Добавить в список воспроизведения (Целую папку | Файл) если находитесь во вкладке 2 (Browser)
Space

# Воспроизвести трек
Backspace

# Воспроизвести выбранный трек
Enter

# Пауза/Воспроизведение
p

# Следующий трек | Предыдущий трек
         Shift+> | Shift+<

# Повторить трек по кругу (необходимо вкл (y) - “Single Mode” только играет один трек, а затем останавливается)
r

# Очистить список воспроизведения (1)
c

# Вкл|Выкл режим вперемешку
z

# Вкл|Выкл режим crossfade (затухание при конце следующего трека)
x

# Обновить базу данных музыки
u

# Разделить окно по ширине (полезно совмещать с вкладкой (8) Визуализатор)
Ctrl+l

# Смена интерфейс (рекомендую classic). В конфиге ncmpcpp определяется как
# user_interface = "alternative"
\

# Информация о треке
i

# Информация о композиторе
Shift+i

# Включить (Найти) текста песни
l

# Выйти
q
```

- [Страница по различным dot файлам ncmpcpp](http://dotshare.it/category/mpd/ncmpcpp/)
- [Cheatsheet страница по ncmpcpp](https://pkgbuild.com/~jelle/ncmpcpp/)

### !Разные полезные штуки

# Открыть ncmpcpp с визуализатором

Прописать в конфиге

```sh
startup_slave_screen = "visualizer"
```

```sh
ncmpcpp -S visualizer
```

## Playerctl + mpd-mpris

playerctl - это MPRIS Media Player контроллер для Spotify, VLC, BMP, XMMS2 и другие

Установка

```sh
sudo pacman -S playerctl
```

Playerctl не поддерживает mpd из коробки, поэтому при нажатии на FN+F6|7|8 никаких действий в mpd не происходит только в браузере

При проверке в терминале

```sh
playerctl next
```

```
output: No players found
```

Есть <font color="green">решение</font> реализации MPRIS протокола для mpd называется [mpd-mpris](https://github.com/natsukagami/mpd-mpris)\
На данный момент mpd-mpris предоставляет:

1. Статус о проигрываемом треке (KDE)
2. Умеет показывать обложки любого трека (если он прописан в тегах)
3. Управление треков мультимедийными клавишами

![image](/images/mpd-with-ncmpcpp/mpDris2_1.png)

![image](/images/mpd-with-ncmpcpp/mpDris2_2.png)

![image](/images/mpd-with-ncmpcpp/mpDris2_3.png)

Установка

```sh
yay -S mpd-mpris
```

Для автозапуска прописываем в `~/.xprofile` (для DM) или `~/.xinitrc` (запуск с TTY)

```sh
mpd-mpris -no-instance &
```

Или включив systemd сервис

```sh
systemctl --user enable --now mpd-mpris
```
