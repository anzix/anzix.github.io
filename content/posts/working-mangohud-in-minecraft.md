+++
title = "MangoHud не работает в Minecraft Java Edition"
date = 2023-03-17T16:36:42+05:00
draft = false
[taxonomies]
categories = ["solving-problem"]
tags = ["linux-gaming"]
+++

- https://github.com/flightlessmango/MangoHud/issues/274

Как бы я ни старался способом прописывания опции `mangohud --dlsym` и `MANGOHUD_DLSYM=1` и даже через lutris, всё это никак не могло заставить работать mangohud в майнкрафте.

Помогло мне предзагрузка библиотек переменной LD_PRELOAD которая принудительно позволяет запустить mangohud в minecraft.

[Что такое `LD_PRELOAD`?](https://blog.fpmurphy.com/2012/09/all-about-ld_preload.html)

Вот 2 способа применения этой переменной

1. TL Legacy

На [TL](https://vk.link/tl_mc_launcher) лаунчере скачанный [AUR пакетом](https://aur.archlinux.org/packages/tlauncher) запускаем данную команду в терминале, активируя mangohud с gamemoderun

```sh
LD_PRELOAD=/usr/lib/mangohud/libMangoHud_dlsym.so:/usr/lib/mangohud/libMangoHud.so gamemoderun /usr/bin/tlauncher
```

Для автоматизации <span style="color:green">рекомендуется</span> отредактировать ярлык TLauncher'а и добавить env с данными переменными

```sh
sudo -e /usr/share/applications/tlauncher.desktop
```

```
[Desktop Entry]
......
Exec=env LD_PRELOAD=/usr/lib/mangohud/libMangoHud_dlsym.so:/usr/lib/mangohud/libMangoHud.so gamemoderun /usr/bin/tlauncher
.....
```

2. Пиратка с rutracker'а

Просто вводим

```sh
LD_PRELOAD=/usr/lib/mangohud/libMangoHud_dlsym.so:/usr/lib/mangohud/libMangoHud.so gamemoderun ~/Downloads/Minecraft_Linux/Minecraft_1.19.2/start.sh
```
