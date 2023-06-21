+++
title = "!DxWrapper OpenSource альтернатива DgVoodoo (установка ручным способом)"
date = 2023-03-04T00:27:06+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

- [DirectDraw to Direct3D9 Conversion](https://github.com/elishacloud/dxwrapper/wiki/DirectDraw-to-Direct3D9-Conversion)

Отличается он тем что даёт чуть больше fps но при этом иногда утилизирует GPU на 80-100% тогда как в DgVoodoo так GPU не юзает для старых игр. На Wayland'е правда лучше не юзать DxWrapper, окно игры не сворачивается и замерзает намертво, либо это как-то фиксится

[Качаем .zip архив](https://github.com/elishacloud/dxwrapper/releases)

Копируем в папку игры

- dxwrapper.ini
- dxwrapper.dll
- d3d9.dll
- d3d8.dll
- ddraw.dll

Открываем dxwrapper.ini и если игра на directx8 меняем значение

```txt
[Compatibility]
....
D3d8to9                    = 1
```

Если же игра на directx7 меняем значение

```txt
[Compatibility]
....
Dd7to9                     = 1
```

Если игра на DirectDraw выставляем значение

```txt
[Compatibility]
....
DDrawCompat                = 1
```

Вообще я <span style="color:green">рекомендую</span> указать все три типа совместимости D3d8to9, Dd7to9 и DDrawCompat изменив на 1 дабы всё сразу работало

Также можно включить vsync (лочит значение фпс до вашего обновления экрана т.е refresh rate)

```txt
[d3d9]
...
EnableVSync                = 1
```

Если VSync не работает включите полноэкранный оконный режим (обычно это мне помогло в Bloodrayne)

```txt
[d3d9]
...
FullscreenWindowMode       = 1
```

В префикс игры обязательно добавляем DXVK

```bash
WINEPREFIX="ПУТЬ/ПРЕФИКСА/ИГРЫ" setup_dxvk install
```

> Если в игре пропадают или глючат текстуры или модели npc попробуйте создать новый префикс и накатить dxvk используя winetricks
> И добавляем переменную переопределения dll в ярлык игры

```bash
WINEDLLOVERRIDES="d3d8,d3d9,d3dimm,ddraw=n,b"
```

Если при запуске игры в Mangohud указан DXVK значит DxWrapper работает

В папке игры также можно посмотреть лог файл dxwrapper-game.log

> Проверено на: [BloodRayne](https://www.pcgamingwiki.com/wiki/BloodRayne)
