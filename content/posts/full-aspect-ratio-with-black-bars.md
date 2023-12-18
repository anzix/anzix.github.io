+++
title = "!Полное соотношение сторон 4:3 с чёрными полосами! (TODO)"
date = 2023-10-09T17:30:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Для этого убираем опцию ``--scale-from`` и добавляем ``--set 'scaling mode' 'Full aspect'``\
Также опцию ``--mode`` меняем на доступное 4:3 разрешение, к примеру ``1024x768``

В конце где xrandr будет возвращать разрешение выстаставляем опцию ``--mode 1920x1080``\
И частоту обновления ``--rate 144``

Пример всех опций для Steam:

```txt
xrandr --output DisplayPort-1 --mode 1024x768 --set 'scaling mode' 'Full aspect' --filter nearest --rate 144 --pos 0x0 --set TearFree off; DXVK_HUD=devinfo gamemoderun mangohud obs-gamecapture %command% -language russian -maxdownloadfilesizemb 999 -novid -nojoy -vulkan -tickrate 72 +clientport 27027 ;xrandr --output DisplayPort-1 --mode 1920x1080 --transform none --dpi 96 --rate 144
```

Манипуляции с xrandr проделанные тут не увенчались успехом

Ярлык рабочего стола созданного Wine'ом:

```txt
...
Exec=xrandr --output DisplayPort-1 --set "scaling mode" "Full aspect"; env obs-gamecapture WINEPREFIX="/home/anix/.local/share/wineprefixes/TH6" mangohud wine C:\\\\users\\\\Public\\\\Desktop\\\\TH06\\ \\~\\ Воплощение\\ Алой\\ Дьяволицы.lnk
....
```

Способ если юзать консоль:

```sh
xrandr --output DisplayPort-1 --set "scaling mode" "Full aspect"; env WINEPREFIX="$HOME/.local/share/wineprefixes/TH8" wine th08\ \(ru\).lnk
```
