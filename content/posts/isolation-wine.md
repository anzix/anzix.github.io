+++
title = "!Мини инструкция по bubblewrap и изолированию wine"
date = 2023-05-25T21:19:09+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Полезные статьи о использовании bubblewrap

- [Arch Wiki Bubblewrap](https://wiki.archlinux.org/title/Bubblewrap)
- [Arch Wiki Bubblewrap/Examples](https://wiki.archlinux.org/title/Bubblewrap/Examples)
- [Первый попавшийся](https://regginator729.wordpress.com/2017/12/12/using-bubblewrap-as-sandbox/)
- [Более полезный, много примеров](https://misile00.github.io/notes/Bubblewrap/)
- [Полезный коментарий Kron4ek](https://www.linux.org.ru/forum/talks/17110355?cid=17110419)
- [Шаблон](https://github.com/IvanMalison/dotfiles-2/blob/master/.bin-bw/wine)

Пояснения по strace - мониторинг системных процессов:

```sh
sudo pacman -S strace
```

Использование: `strace bwrap ...`

Пояснение:

- fork - создаёт новый дочерний процесс
- read - попытается прочитать с файлового дескриптора
- write - попытается записать файловый дескриптор
- openat - открывает файл для чтения или записи
- close - закрывает файл после чтения или записи
- chdir - изменение текущей директории
- execve - выполняет исполняемый файл
- fstat - получает инфу о файле
- mknod - создаёт спец. файл (например файл устройства или сокета)
- mmap - запрос на выделение памяти

Изоляция wine ???

```sh
bwrap --unshare-net --ro-bind / / --bind /home /home wine ...
```

ВАЖНО!!

Делится `/usr/share/` и `/etc` - опасно!

Также нельзя делится `/var` т.к там содержатся данные такие как `/var/log` которые чрезвычайно чувствительные

```txt
В: Что делает `--dev-bind`?
О:

В: Что делает `--bind`?
О: Монтирует заданный путь к цели в изолированной среде

В: Что делает `--ro-bind`?
О: Монтирует заданный путь к цели с доступом только для чтения в изолированной среде

В: Что делает `--dev /dev`?
О: Монтрует /dev но не целиком а только часть его т.е /dev/sda и /dev/ttyX не будет показыватся в изоляции. Однако нужно ли это в использовании wine??
```

bwrap опции:

- `--ro-bind / /` - Сделать весь root только для чтения (каталоги не создаются в /home)
- `--unshare-net` - не будет доступа в инет
- `--unshare-pid` - скрывает все процессы pid
- `--die-with-parent` - при закрытии bwrap все (дочерний т.е child) процессы будут убиты
- `--tmpfs /tmp` - необходимо чтобы избежать "wineserver: mkdir /tmp/.wine-1000: Read-only file system" (на хосте tmp у меня смонтирован как tmpfs)
- ` --proc /proc` - необходим чтобы избежать "wine: could not load ntdll.so: (null)". Отвечает за связь программ с ядром. Проще говоря, она работает с процессами.
- `--dev-bind /dev/snd /dev/snd` - необходимо для поддержки звука ALSA в wine (`winecfg` для тестирования звука)
- `--tmpfs /run --bind "/run/user/$UID/bus" "/run/user/$UID/bus"` - чтобы работал dbus, архиватор ark ругается без него. Включает drag-n-drop

Необходимы для аппаратного ускорения (не проверено)

- `--ro-bind /sys/dev /sys/dev`
- `--ro-bind /sys/devices /sys/devices`

Необходимо чтобы шрифты подхватывались и чтобы избежать "Fontconfig error: Cannot load default config file: No such file: (null)"

- `--ro-bind /etc/fonts /etc/fonts`

[Arch Wiki](https://wiki.archlinux.org/title/Bubblewrap#Using_X11) (Для X11 сессии) Без этих двух доп опций wine не будет выводить графические приложения, и они не запустятся. Будет выводить данный лог

```txt
010c:err:winediag:nodrv_CreateWindow Application tried to create a window, but no driver could be loaded.
010c:err:winediag:nodrv_CreateWindow L"Make sure that your X server is running and that $DISPLAY is set correctly."
```

- `--bind /tmp/.X11-unix/X0 /tmp/.X11-unix/X0`
- `--setenv DISPLAY :0`

(если Wayland) необходимо смонтировать сокет Wayland, чтобы выводить графические приложения

Для использования bubblewrap в AppImage, его необходимо распаковать данной командой

```sh
./*.AppImage --appimage-extract
```

Перемонтирую /home в виде tmpfs, чтобы игра не увидела реальный /home (не проверено)

- `--tmpfs /home --bind $HOME/new_home $HOME`

Конечный миниамалистичный изолятор wine `cat sandbox`

```sh
#!/bin/bash

wine="$(which wine)"

# Ипользование с default префиксом
# ./my-isolate-wine
# С кастомным перфиксом
# WINEPREFIX=$HOME/.local/share/wineprefixes/PREFIX_NAME ./my-isolate-wine

set -euETo pipefail
shopt -s inherit_errexit
set -x

# export WINEARCH=win64 # Архитектура префикса
# export WINEDEBUG=-all,+err,+warn

mkdir -p "$WINEPREFIX" 2>/dev/null
# mkdir -p "$XDG_CACHE_HOME/winetricks" 2>/dev/null

exec bwrap \
 --unshare-net `# нет инета` \
 --ro-bind /usr /usr `# находятся исполняемые файлы` \
 --ro-bind /usr/lib /usr/lib `# чтобы подхватывались бибилиотеки` \
 --ro-bind /etc/fonts /etc/fonts `# чтобы шрифты подхватывались` \
 --symlink /usr/bin /bin `# копия стандартной структуры фс linux` \
 --symlink /usr/bin /sbin `# копия стандартной структуры фс linux` \
 --symlink /usr/lib /lib `# копия стандартной структуры фс linux` \
 --symlink /usr/lib /lib64 `# копия стандартной структуры фс linux` \
 --proc /proc `# чтобы создавались процессы` \
 --dev /dev `# частичное монтирование /dev, без чувствительных данных по типу /dev/sda` \
 --dev-bind /dev/dri /dev/dri `# видеоадаптеры` \
 --dev-bind /dev/snd /dev/snd `# чтобы работал звук ALSA` \
 --tmpfs /tmp \
 --tmpfs /run --bind "/run/user/$UID/bus" "/run/user/$UID/bus" `# чтобы работал dbus` \
 --tmpfs "$HOME" \
 --ro-bind "$HOME/Downloads" "$HOME/Downloads" \
 --bind /tmp/.X11-unix /tmp/.X11-unix `# чтобы показывались граф. программы` \
 --bind $XAUTHORITY $XAUTHORITY \
 --bind "$WINEPREFIX" "$WINEPREFIX" `# ` \
 --die-with-parent `# при закрытии bwrap все процессы будут убиты` \
 wine "$@"
 #pcmanfm
```
