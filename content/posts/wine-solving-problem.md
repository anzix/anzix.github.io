+++
title = "Решение проблем с Wine/Proton"
date = 2023-12-11T17:30:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux-gaming"]
+++

## Указанный файл не был найден (wine)

- [Источник](https://forum.winehq.org/viewtopic.php?t=15267)

![image](/images/wine-solving-problem/error.png)

Повторный запуск с указанием полного пути autorun.exe решило проблему

В итоге неправильно это

```sh
/run/media/anix/worms2 ❯ WINEPREFIX="$HOME/.local/share/wineprefixes/Worms" wine autorun.exe
```

Правильно

```sh
WINEPREFIX="$HOME/.local/share/wineprefixes/Worms" wine /run/media/anix/worms2/autorun.exe
```

## Некоторые старые игры не запускаются от .exe файла

При запуске .exe файла разрешение экрана меняется на 640x480 и игра вылетает

Мне помог данный [видео гайд на отметке 11:12](https://youtu.be/J_L9LB3o5SQ?t=672)

На примере Battle Engine Aquila репак от Механиков\
Необходимо было переустановить игру и в установщике отметить создание ярлыка игры на раб столе

Решение:\
Нужно было запускать .lnk (Win) файл (находящийся внутри префикса игры ``<префикс_игры>/drive_c/users/Public/Desktop/<ярлык_игры>.lnk`` а не .exe внутри установленной игры и тогда игра запуститься

```sh
WINEPREFIX="$HOME/.local/share/wineprefixes/BEAquila" wine $HOME/Desktop/Battle\ Engine\ Aquila.lnk
```

## Артефакты используя Wine в старой игре

При переключении раб. стола в оконном менеджере i3wm в запущенным ОБЫЧНОМ wine'ом игре Freedom Fighters появляются артефакты

![image](/images/wine-solving-problem/normal-texture.png)

![image](/images/wine-solving-problem/artifacts.png)

Полноценное решение - это использовать связку DgVoodoo+DXVK

[Скачать последнюю версию DgVoodoo](http://dege.freeweb.hu/dgVoodoo2/dgVoodoo2/) и при распаковки закинуть все dll'ки из **/dgVoodoo2_79_3/MS/x86/** в папку с игрой

Что бы DgVoodoo заработал необходим DXVK, качаем AUR пакет dxvk-bin

```sh
yay -S dxvk-bin
```

После установки накатываем dxvk в префикс (Freedom Fighters)

```sh
WINEPREFIX="$HOME/.local/share/wineprefixes/FF" setup_dxvk install
```

Готово! Теперь запускаем игру, я для запуска использую терминал

```sh
DXVK_FRAME_RATE=60 obs-gamecapture mangohud gamemoderun WINEDLLOVERRIDES="d3d8=n,b;d3d9=n,b;d3dimm=n,b;ddraw=n,b" WINEPREFIX="$HOME/.local/share/wineprefixes/FF" wine $HOME/.local/share/wineprefixes/FF/drive_c/users/Public/Desktop/Freedom\ Fighters.lnk
```

## Warcraft 3 The Frozen Throne 32bit нет звука (Pipewire)

- [Источник](https://www.reddit.com/r/archlinux/comments/p9itwq/audio_issues_with_pipewire_on_older_game/)

Вот логи Wine

```txt
ALSA lib dlmisc.c:337:(snd_dlobj_cache_get0) Cannot open shared library libasound_module_pcm_pipewire.so (/usr/lib32/alsa-lib/libasound_module_pcm_pipewire.so: невозможно открыть разделяемый объектный файл: Нет такого файла или каталога)
ALSA lib dlmisc.c:337:(snd_dlobj_cache_get0) Cannot open shared library libasound_module_pcm_pipewire.so (/usr/lib32/alsa-lib/libasound_module_pcm_pipewire.so: невозможно открыть разделяемый объектный файл: Нет такого файла или каталога)
```

Решением было установить

```sh
sudo pacman -S lib32-pipewire
```

## Warcraft 3 The Frozen Throne не запускается

- [Источник_1](https://forum.winehq.org/viewtopic.php?p=137743#p137743)
- [Источник_2](https://forum.winehq.org/viewtopic.php?p=130430#p130430)

При запуске сразу же закрывалась с выводом данной ошибки

```txt
(wine:529528): GStreamer-CRITICAL **: 17:46:55.114: gst_object_unref: assertion 'object != NULL' failed
winegstreamer error: decodebin1: В вашей установке GStreamer отсутствует модуль.
winegstreamer error: decodebin1: ../gstreamer/subprojects/gst-plugins-base/gst/playback/gstdecodebin2.c(4701): gst_decode_bin_expose (): /GstBin:bin2/GstDecodeBin:decodebin1:
no suitable plugins found:
Missing decoder: Audio Video Interleave (AVI) (video/x-msvideo)
```

Помогло решить проблему установка данного пакета (выбрал репозиторий multilib: lib32-pipewire-jack)

```sh
sudo pacman -S lib32-gst-plugins-good
```

Доп-но установятся данные пакеты

```txt
lib32-libsamplerate (0.2.2-1)
lib32-jack2 (1.9.21-1)
lib32-libnghttp2 (1.50.0-1)
lib32-libsoup3 (3.2.1-1)
lib32-wavpack (5.5.0-2)
lib32-gpm (1.20.7-2)
lib32-aalib (1.4rc5-3)
lib32-taglib (1.13-1)
lib32-popt (1.18-1)
lib32-libdv (1.0.0-6)
lib32-speex (1.2.1-1)
lib32-libshout (2.4.6-1)
lib32-libvpx (1.12.0-1)
lib32-imlib2 (1.9.1-1)
lib32-libcaca (0.99.beta20-1)
lib32-libraw1394 (2.1.2-3)
lib32-libavc1394 (0.5.4-3)
lib32-libiec61883 (1.2.0-3)
lib32-libgudev (237-2)
lib32-twolame (0.4.0-2)
lib32-gst-plugins-good (1.20.4-1)
```

## Ошибка установщика репака Mechanics используя wine: unarc.dll error -5: not enough memory

- [Источник](https://github.com/DarkReaperBoy/darkreaperboy.github.io/blob/main/draft/gamingonlinux/Repackers.md)

![image](/images/wine-solving-problem/unarc_error.png)

Решение [собрать wine-tkg](https://github.com/Frogging-Family/wine-tkg-git) (заменится wine-staging) и использовать его\
Или использовать [wine-7.18-staging-tkg-amd64](https://github.com/Kron4ek/Wine-Builds/releases) от [Kron4ek](https://github.com/Kron4ek) в из репо [Wine-Builds](https://github.com/Kron4ek/Wine-Builds)

Необходимо распаковать архив и добавить в запуск бинарника wine по такому пути

```sh
WINEPREFIX="$HOME/.local/share/wineprefixes/BEAquila" ~/Downloads/wine-7.18-staging-tkg-amd64/bin/wine /media/Distrib/Игры/OLD\ GAMES/\[R.G.\ Mechanics\]\ Battle\ Engine\ Aquila/setup.exe
```

Установка репака от Механиков игры Battle Engine Aquila прошла успешно

## !Не работает геймпад в старых игрых!

- [Источник_1](https://kubuntu.ru/node/14630)
- [Источник_2](https://blog.psy-q.ch/blog/2017/12/26/using-xbox-360-compatible-controllers-properly-inside-wine/)

На игре [TMNT 2: Battle Nexus](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles_2:_Battle_Nexus), [TMNT: Melee](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles:_Mutant_Melee), [TMNT 2003](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles_(2003)) PC - не работает управление с геймпада

## Некорректное отображение шрифтов в Wine

![image](/images/wine-solving-problem/incorrect-fonts.png)

```sh
cd ${WINEPREFIX:-~/.local/share/wineprefixes/hexedit}/drive_c/windows/Fonts && for i in /usr/share/fonts/**/*.{ttf,otf}; do ln -s "$i" ; done
```

Данный метод описанный [первым в Arch Wiki](https://wiki.archlinux.org/title/Wine#Fonts) не работает, видимо нужно скачать шрифты винды

Решил благодаря данной переменной

```sh
FREETYPE_PROPERTIES="truetype:interpreter-version=35"
```

Запуск:

```sh
FREETYPE_PROPERTIES="truetype:interpreter-version=35" WINEPREFIX=$HOME/.local/share/wineprefixes/hexedit wine $HOME/.local/share/wineprefixes/hexedit/drive_c/Program\ Files\ \(x86\)/Plexer\ Code/ResHex/ResHex.exe
```

![image](/images/wine-solving-problem/correct-fonts.png)
