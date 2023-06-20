+++
title = "Конвертирование образов для эмуляторов на Linux (не завершён)"
date = 2023-03-02T18:56:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

[Видео гайд по chd конвертеру](https://www.youtube.com/watch?v=DGKTCox6SyY)
[Видео гайд по всем конвертерам для эмуляторов](https://www.youtube.com/watch?v=LehvqHycejc)

```bash
sudo pacman -S mame-tools
yay -S ciso
yay -S makerom-git
yay -S extract-xiso-git
```

Пояснение:
* mame-tools - Конвертирование .iso / .cue образов PS2 игр в сжатый .chd образ (chdman)
* ciso - конвертирование из iso в cso для PSP образов
* makerom-git - уменьшает размер для образов 3DS
* extract-xiso-git - конвертирует Xbox/Xbox360 iso образы в xiso для xemu эмулятора

Функции (вставляем в .zshrc):
```bash
# Конвертирование образов
# Использование: convert2chd [файл]
convert2chd(){ chdman createcd -i "$1" -o "${1%.*}.chd" ;}
chd2cue(){ chdman extractcd -i "$1" -o "${1%.*}.cue" ;}
iso2cso(){ ciso 9 "$1" "${1%.*}.cso" ;}
```

Используя convert2chd образ .iso God Hand (1.5GB) сжат до 1.1GB
