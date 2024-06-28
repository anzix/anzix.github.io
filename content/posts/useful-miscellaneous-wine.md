+++
title = "!WINE разное полезное"
date = 2023-10-20T15:20:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

## Авто процесс создания архивированных резервных копий файлов сохранений игры

Например я хочу упростить или сделать автоматическим процесс создания\
архивированных резервных копий файлов сохранений одиночной игры.\
Я бы хотел, чтобы у них была временная метка, чтобы они не перезаписывали друг друга.

Вероятно, лучше всего запустить процесс архивации после завершения вашей игры. Напр.

Скачиваем пакет `zip`

```sh
sudo pacman -S zip
```

Архивируем сейвы игры когда игра закрывается, zip архив будет находится в $HOME

```sh
wine thegame.exe && zip -r "saves-$(date +%s).zip" /путь_до_saves/
```

Вариант для `.desktop` ярлыка lutris, на примере игры Lego Harry Potter: Years 1-4

```txt
...
Exec=env LUTRIS_SKIP_INIT=1 lutris lutris:rungameid/1 && zip -r "saves-$(date +%s).zip" "/home/user/Games/gog/lego-harry-potter-years-1-4/drive_c/users/anix/AppData/Roaming/WB Games/LEGO® Harry Potter™/SavedGames"
...
```

## (X11 Xorg) Ручное восстановление гаммы при выходе из игры wine

- [Источник_1](https://steamcommunity.com/app/253980/discussions/0/2741975115063956992/)
- [Источник_2](https://github.com/lutris/lutris/issues/1010#issuecomment-411502044)

Скачиваем данный пакет

```sh
sudo pacman -S xorg-xgamma
```

И вводим для восстановления гаммы

```sh
xgamma -gamma 1
```

## Wine. Запуск bat-файлов («батников») 2 варианта

Их запуск отличается от запуска exe-файлов.

1. Пример запуска script.bat:

```sh
WINEPREFIX="/home/$USER/.wine" wine start /unix "/home/$USER/.wine/drive_c/script.bat"
```

2. Открыть *.bat файл ``wineconsole cmd`` или `wine cmd`

И в нём уже ввести ``script.bat``

## Удаление конкретной программы без использования GUI

Вот как это применяется используя команду, регистр имени программы не имеет значения

```sh
WINEPREFIX="/home/$USER/.wine" wine uninstaller --remove название_программы
```

После удаление программ из wine, оставшиеся файлы находятся

```txt
~/.local/share/applications/wine/
~/.local/share/icons/hicolor/
```

После удаления иконок необходимо выполнить команду

```sh
update-desktop-database ~/.local/share/applications
```

## Вывод логов в wine

Вывести в файл wine-log.txt только ошибки и предупреждения в ходе выполнения программы:

```sh
WINEDEBUG=-all,+err,+warn WINEPREFIX="/home/$USER/.wine" wine "C:/games/my_game/game.exe" &> wine-log.txt
```

Вывести в файл wine-dll-log.txt список Windows-библиотек (dll), используемых в процессе запуска программы:

```sh
WINEDEBUG=+loaddll WINEPREFIX="/home/$USER/.wine" wine notepad &> wine-dll-log.txt
```

## Смена версии совместимости wine через cli (winxp / win7 / win10)

Для этого используется winetricks

```sh
WINEPREFIX="prefix_name" winetricks win10
```

## Как удалить библиотеки которые были установлены через winetricks?

В `winecfg` разделе библиотеки те что `*` удаляем и всё востановлено

## Интересное применение переопределений для wine

```sh
WINEDLLOVERRIDES="d3d...=n;dxgi=n;quartz=n,b;"
```

## Создание префикса wine в текущей директории

```sh
WINEPREFIX=\$(pwd)
```

## Применение dll'ок утилитой regsvr32

На примере Warcraft III

```sh
regsvr32 $HOME/Downloads/WarcraftIII_Linux/WarcraftIII_1.26a/game_info/da
ta/blizzard.ax
```

```txt
regsvr32: DLL «$HOME/Downloads/WarcraftIII_Linux/WarcraftIII_1.26a/game_info/da
ta/blizzard.ax» успешно зарегистрирована
```

## Ubisoft аргументы запуска игры при выполнении Ubisoft Connect

Вот как это делается

```sh
WINEPREFIX="prefix" wine UbisoftConnect.exe uplay://launch/273/0
```

## (Не проверено) Слишком быстрые или медленные старые игры под wine

Решение использовать утилиту cpulimit чтобы ограничить процент использования процессора. Например:

```sh
cpulimit -l 50 wine game.exe
```

Доступные значения от 0 до (количество ядер * 100). То есть если у тебя два ядра, то максимальное значение - 200.

## Лёгкий способ вызвать AUTORUN.exe из смонтированного образа используя wine

Вызываете деинсталлятор данной командой

```sh
WINEPREFIX="$HOME/.local/share/wineprefixes/[ПРЕФИКС_ИГРЫ] wine uninstaller
```

Жмёте "Установить" - указываете путь к смонтированному образу и запускаете AutoRun.exe

