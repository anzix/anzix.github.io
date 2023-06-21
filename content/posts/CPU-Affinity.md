+++
title = "Ограничение использования ядер процессора для игры/программы (CPU Affinity)"
date = 2023-03-03T21:12:33+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++
Поставляется taskset пакетом [util-linux](https://www.archlinux.org/packages/core/x86_64/util-linux/)

Для примера я продемонстрирую использование 4 (0-3) ядра на игре Sims 2, хоть она и без этого работает замечательно
## Desktop Entry (Ярлык)
Для ярлыка вставляем ``taskset -c 0-3`` до команды wine
```bash
....
Exec=env DXVK_HUD=compiler obs-gamecapture mangohud gamemoderun WINEPREFIX="$HOME/.local/share/wineprefixes/SIMS2" taskset -c 0-3 wine Z:\\\\home\\\\anix\\\\Games\\\\The\\ Sims\\ 2\\ -\\ Seasons\\\\sims2seasons\\\\TSBin\\\\Sims2EP5.exe
....
```
## Через терминал
Здесь уже вставляем ``& taskset -cp 0-3 $!`` в конце
```bash
DXVK_HUD=compiler obs-gamecapture mangohud gamemoderun WINEPREFIX=$HOME/.local/share/wineprefixes/SIMS2 wine /home/anix/Desktop/The\ Sims\ 2\ -\ Seasons.lnk & taskset -cp 0-3 $!
```
Если увидите такой выхлоп - значит taskset начал работать.
```sh
....
[1] 852088
pid 852088's current affinity list: 0-11
pid 852088's new affinity list: 0-3
....
```
## (Steam) Параметры запуска
> Примечание: Не указывайте ``taskset -c 0-3`` после ``%command%``, иначе у вас игра не запустится
```bash
DXVK_HUD=compiler obs-gamecapture gamemoderun mangohud taskset -c 0-3 %command%
```
Узнать что taskset работает можно благодаря мониторингу MangoHud
![](/images/CPU-Affinity/taskset-mangohud.png)
