+++
title = "Добавление и замена пасскея (passkey) RuTracker'а в .torrent файл с помощью transmission-cli"
date = 2023-03-04T20:29:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
Источники:
* https://rutracker.org/forum/viewtopic.php?p=69547524#69547524
* https://rutracker.org/forum/viewtopic.php?p=80460785#80460785

Данное решение распространяется для всех торрент клиентов (transmission, qbittorrent и другие)

Качаем пакет
```bash
sudo pacman -S transmission-cli
```

> **Внимание**: Не забывайте про бэкапы .torrent файлов!
```bash
tar -cvzf torrents-transmission.tar.gz ~/.config/transmission/torrents/
```

## Использование для одного .torrent файла:
Загружен зарегистрированным но без passkey'я внутри
```bash
transmission-edit --replace t-ru.org/ann t-ru.org/ann?pk=[ваш пасскей] [.torrent файл]
```

Использование если .torrent файлов внутри папке много, то в цикле вводим
```bash
for f in ./* ; do transmission-edit --replace t-ru.org/ann t-ru.org/ann?pk=[ваш пасскей] "$f" ; done
```

## Загруженный через manget
```bash
transmission-edit --replace t-ru.org/ann?magnet t-ru.org/ann?pk=[ваш пасскей] [.torrent файл]
```
