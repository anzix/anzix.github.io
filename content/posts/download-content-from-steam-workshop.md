+++
title = "Как отдельно скачать контент/моды из Steam Workshop на Arch Linux"
date = 2023-03-07T11:00:37+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++
* https://steamcommunity.com/sharedfiles/filedetails/?id=873543244

Запускаем в терминале steam с опцией -console
```bash
steam -console
```
После чего у вас появится соответствующий раздел, переходим в Console

Для скачивания контента из Steam Workshop необходимо наличие купленной игры в Стиме

Применение:
```bash
workshop_download_item [id app] [id контента]
```

Вводим на примере скачивания [живых обоев из Wallpaper Engine](https://steamcommunity.com/sharedfiles/filedetails/?id=2884223898)
```bash
workshop_download_item 431960 2884223898
```

В стиме начнётся скачка этого контента

После скачивания он будет сохранён по данному пути
```bash
$HOME/.steam/steam/steamapps/workshop/content/[id app]/[id контента]
```

То есть
```bash
$HOME/.steam/steam/steamapps/workshop/content/431960/2884223898
```
## Используя Steamcmd

[Инструмент в виде сайта](https://steamworkshopdownloader.io/) (позволяет скачивать контент только с анонимной сессией, а остальное предоставляет инструкцию по установке steamcmd)
* [Офф вики по steamcmd](https://developer.valvesoftware.com/wiki/SteamCMD#Downloading_SteamCMD)

Качаем [AUR пакет](https://aur.archlinux.org/packages/steamcmd) вместе с доп библиотеками
```bash
yay -S steamcmd lib32-sdl2 lib32-dbus
```

Если контент который вы ходите скачать позволяет

Запускаем в терминале и ждём обновления
```bash
steamcmd
```

Если у вас открыт Steam и вы уже залогинены тогда просто вводим
```bash
login "username"
```

Если же нет тогда у вас попросят пароль и steam Guard

Применение
```bash
workshop_download_item [id app] [id контента]
```

После скачивания он будет сохранён $HOME/.local/share/Steam/steamapps/workshop/content/[id_game]/[id_content]

Пример
```bash
workshop_download_item 730 2807727155
```

