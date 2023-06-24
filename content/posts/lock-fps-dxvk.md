+++
title = "Лимит FPS при помощи DXVK"
date = 2023-03-04T18:10:32+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Если в префиксе установлен dxvk
Использовать данную переменную в терминале или параметре запуска Steam

```bash
DXVK_FRAME_RATE=60 / 30 / 0 - без лимита fps
```

Для старых игр у которых проблема с физикой и звуком (на примере [Harry Potter and the Philosopher's Stone PC](https://www.pcgamingwiki.com/wiki/Harry_Potter_and_the_Philosopher%27s_Stone)), <span style="color:green">рекомендуется</span> использовать связку с DgVoodoo + DXVK и данной переменной при запуске DXVK_FRAME_RATE=60
