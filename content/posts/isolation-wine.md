+++
title = "!Мини инструкция по bubblewrap и изолированию wine"
date = 2023-05-25
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

Также нельзя делится `/var` т.к там содержатся данные такие как `/var/log`
которые чрезвычайно чувствительные

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

- `--ro-bind / /` - Сделать весь root только для чтения (каталоги не создаются
  в /home)
- `--unshare-net` - не будет доступа в инет
- `--unshare-pid` - скрывает все процессы pid
- `--die-with-parent` - при закрытии bwrap все (дочерний т.е child) процессы
  будут убиты
- `--tmpfs /tmp` - необходимо чтобы избежать "wineserver: mkdir /tmp/.wine-1000:
  Read-only file system" (на хосте tmp у меня смонтирован как tmpfs)
- `--proc /proc` - необходим чтобы избежать "wine: could not load ntdll.so: (null)".
  Отвечает за связь программ с ядром. Проще говоря, она работает с процессами.
- `--dev-bind /dev/snd /dev/snd` - необходимо для поддержки звука ALSA в wine
  (`winecfg` для тестирования звука)
- `--tmpfs /run --bind "/run/user/$UID/bus" "/run/user/$UID/bus"` - чтобы
  работал dbus, архиватор ark ругается без него. Включает drag-n-drop
- `--ro-bind /sys/dev /sys/dev`, `--ro-bind /sys/devices /sys/devices` - Необходимы
  для аппаратного ускорения (не проверено)
- `--ro-bind /etc/fonts /etc/fonts` - Необходимо чтобы шрифты подхватывались и
  чтобы избежать "Fontconfig error: Cannot load default config file: No such
  file: (null)"
- `--bind /tmp/.X11-unix/X0 /tmp/.X11-unix/X0`, `--setenv DISPLAY :0` - Для X11
  сессии, без этих двух доп опций wine не будет выводить графические
  приложения, и они не запустятся. Будет выводить данный лог

  ```txt
  010c:err:winediag:nodrv_CreateWindow Application tried to create a window, but no driver could be loaded.
  010c:err:winediag:nodrv_CreateWindow L"Make sure that your X server is running and that $DISPLAY is set correctly."
  ```

  [Источник Arch Wiki](https://wiki.archlinux.org/title/Bubblewrap#Using_X11)

- Если Wayland, то необходимо смонтировать сокет Wayland, чтобы выводить графические
  приложения (TODO: как это сделать?)

- `--tmpfs /home --bind $HOME/new_home $HOME` - Перемонтирую /home в виде
  tmpfs, чтобы игра не увидела реальный /home (TODO: не проверено)

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

Вместо wine я на скриншоте смог запустить pcmanfm

![image](/images/isolation-wine/Screenshot_20230519_162447.png)

## Использование для AppImage

Для использования bubblewrap в AppImage, его необходимо распаковать данной командой

```sh
./*.AppImage --appimage-extract
```

## Скрипт для изоляции wine

TODO: Проверить

101796281:
Myself i just made shell scripts which launch games using the system install of
wine through bubblewrap (sandbox, the one flatpaks use). Naturally this isn't
as "nice" as lutris, but it uses minimal resources, and all i need is a desktop
icon to run the game. Bubblewrap uses about a meg, wine about 180M (being the
system wine it will use less, as it's using shared system libraries and not a
runtime like lutris or steam), then the game uses what it normally uses

101806508:
you may need to adjust it a bit for your distro, like the lib symlinks

```sh
#!/bin/env sh
if [ "$(which mangohud)" != "" ]; then mangohud="mangohud"; fi
#if [ "$(which gamescope)" != "" ]; then gamescope="gamescope -w 1920 -h 1080 -r 72 -F fsr -f --"; fi
bwrap   --ro-bind /usr /usr \
        --tmpfs /usr/local/bin \
        --symlink usr/lib64 /lib64 \
        --symlink usr/lib /lib \
        --symlink usr/bin /bin \
        --proc /proc \
        --ro-bind-try /sys/dev/char /sys/dev/char \
        --ro-bind-try /sys/devices /sys/devices \
        --ro-bind-try /sys/class /sys/class \
        --dev /dev \
        --dev-bind-try /dev/snd /dev/snd \
        --dev-bind-try /dev/dri /dev/dri \
        --dev-bind-try /dev/input /dev/input \
        --dev-bind-try /dev/shm /dev/shm \
        --ro-bind /etc /etc \
        --tmpfs /tmp \
        --ro-bind-try /tmp/cdrom /tmp/cdrom \
        --bind-try /tmp/store/Games/Executable /tmp/store/Games/Executable \
        --bind /tmp/.X11-unix /tmp/.X11-unix \
        --bind . $HOME \
        --dir $HOME/install \
        --ro-bind $HOME/.themes $HOME/.themes \
        --ro-bind $HOME/.gtkrc-2.0 $HOME/.gtkrc-2.0 \
        --dir $HOME/.config \
        --ro-bind $HOME/.config/fontconfig $HOME/.config/fontconfig \
        --ro-bind $HOME/.fonts $HOME/.fonts \
        --ro-bind $XAUTHORITY $XAUTHORITY \
        --ro-bind /run/user/$(id -u)/pulse /run/user/$(id -u)/pulse \
        --ro-bind /run/user/$(id -u)/pipewire-0 /run/user/$(id -u)/pipewire-0 \
        --cap-drop all \
        --cap-add CAP_SYS_NICE \
        --unshare-all \
        --die-with-parent \
        --setenv WINEDEBUG "" \
        --setenv WINEARCH win64 \
        --setenv WINEDLLOVERRIDES "" \
        --setenv WINE_FULLSCREEN_FSR 1 \
        --chdir "$HOME/A Hat in Time/Binaries/Win64" \
        sh -c " \
            echo fish; \
            echo wine control; \
            $gamescope $mangohud wine HatinTimeGame.exe; \
            wineserver -k; \
        "
```
