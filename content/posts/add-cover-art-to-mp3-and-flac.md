+++
title = "Добавление обложки в трек (Mp3/FLAC)"
date = 2023-03-08T19:43:02+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

- [Источник](https://unix.stackexchange.com/a/320406)

## Flaс

Пакет metaflaс обычно уже предустановлен в Arch Linux

Синтаксис

```sh
metaflac --import-picture-from=[путь к обложке] [путь к треку]
```

Примеры

```sh
metaflac --import-picture-from=folder.jpg DΛRKNΣSS\ -\ 02\ -\ your\ victim.flac
# Или
metaflac --import-picture-from='/media/Media/Other music/Games/Susumu Hirasawa - Berserk (1999,2004) [Flac]/1999 - Berserk Millennium Falcon Arc - Chapter of the Oblivion Flower Original Game Soundtrack/Scans/01.jpg' *
```

Работает также и со всеми аудио файлами в папке

```sh
metaflac --import-picture-from=folder.jpg *
```

Подобные вывод просто игнорьте

```txt
folder.jpg: ERROR: reading metadata, status = "FLAC__METADATA_CHAIN_STATUS_NOT_A_FLAC_FILE"
```

## Mp3 (не пробовал)

Качаем пакет

```sh
sudo pacman -S python-eyed3
```

Использование

```sh
eyeD3 --add-image="cover.jpg":FRONT_COVER "file.mp3"
```
