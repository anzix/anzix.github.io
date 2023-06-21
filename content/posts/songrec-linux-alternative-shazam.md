+++
title = "Songrec - Linux альтернатива Shazam"
date = 2023-03-22T01:40:27+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Определяет очень быстро, может опознать ноунейм композиции но не всегда

Установка
```sh
sudo pacman -S songrec
```

После запуска он сразу включает распознавание

Стандартно идёт запись с микрофона

Для распознавания со внутреннего звука ПК нужно запустить pavucontrol и во вкладке Запись (не забываем включить распознавание в songrec) выставить откуда output звука исходит (в моём случае через мониторный выход jack микрофона samson c10u pro на наушники).

![](/images/songrec-linux-alternative-shazam/songrec-changing-source.png)

## Использование с терминала

```sh
songrec recognize -d pipewire | xsel -b -i && notify-send -t 25000 "Song recognized" "xsel -b" -u low
```
Опции:
* `-d [Аудио вход]`

