+++
title = "!Как спамить звуками используя микрофон в играх (не проверено)"
date = 2023-03-22T19:43:05+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++
Источники:
* https://steamcommunity.com/sharedfiles/filedetails/?id=249772871
* https://steamcommunity.com/sharedfiles/filedetails/?id=2887423350

Вводите
```sh
pactl load-module module-null-sink
```

Открываете `pavucontrol` и меняете источник в **Проигрывание** на **null-sink Audio/Sink sink**

Чтобы убрать модуль null-sink
```sh
pactl unload-module module-null-sink
```
