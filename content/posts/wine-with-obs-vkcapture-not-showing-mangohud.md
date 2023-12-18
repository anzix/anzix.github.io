+++
title = "При использовании obs-vkcapture с wine в OBS не показывается mangohud (старая игра)"
date = 2023-10-09T17:35:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux-gaming"]
+++

Игра [Shrek 2 The Game](https://www.pcgamingwiki.com/wiki/Shrek_2:_The_Game), в самой игре mangohud есть но в превью obs и на записи его нету

Играл в Shrek 2 The Game с такими параметрами из под терминала

```sh
DXVK_HUD=compiler vk_radv obs-gamecapture gamemoderun mangohud WINEPREFIX="$HOME/.local/share/wineprefixes/Shrek_2" wine $HOME/Games/Shrek\ 2/System/game.exe
```

> Примечание: Запускал с установленным в префикс для игры dxvk (но он не работал). Так и без dxvk

Obs запускал с такими опциями из под терминала ``OBS_USE_EGL=1 obs``

Решил проблему благодаря DgVoodoo, необходимо [скачать последнюю версию DgVoodoo](http://dege.freeweb.hu/dgVoodoo2/dgVoodoo2/) и при распаковки закинуть все dll'ки из **/dgVoodoo2_79_3/MS/x86/** в папку с игрой и прописать определение используя переменную ``WINEDLLOVERRIDES="d3d8,d3d9,d3dimm,ddraw=n,b"`` при запуске игры

Запустив с данными опциями и переменными:

```sh
DXVK_HUD=compiler obs-gamecapture gamemoderun mangohud WINEDLLOVERRIDES="d3d8=n,b;d3d9=n,b;d3dimm=n,b;ddraw=n,b"  WINEPREFIX="$HOME/.local/share/wineprefixes/Shrek2" wine $HOME/Desktop/Shrek\ 2.lnk
```
