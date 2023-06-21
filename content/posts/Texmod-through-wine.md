+++
title = "Texmod через Wine в Linux"
date = 2023-03-03T14:38:32+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++
На примере NFS Underground 2 High_Vision_1.10

Если вы скопировали все файлы включая Texmod.exe внутрь папки игры, запускаем обычным способом через терминал указывая ваш префикс и exe'шник Texmod:
```bash
mangohud obs-gamecapture WINEPREFIX=$HOME/.local/share/wineprefixes/NFSU2 wine $HOME/Games/Need\ for\ Speed\ Underground\ 2/Texmod.exe
```

Если вы ранее до Linux имели дело с Texmod на Винде, то с wine процесс будет похожим

![](/images/Texmod-through-wine/texmod-1.png)
![](/images/Texmod-through-wine/texmod-2.png)

Из списка выбираем модификацию и нажимаем Run

Ваш рабочий стол станет пустым (только обои) и вам предстоит только ждать т.к это специфика использования Texmod. В общем как на Шинде

Ждать мне предстояло 2 минуты 11 секунд и игра запустится

