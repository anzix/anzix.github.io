+++
title = "!DgVoodoo и как его использовать вместе с DXVK для старых игр (ручной способ)"
date = 2023-03-04T17:14:24+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++

- [Полезный видео гайд](https://www.youtube.com/watch?v=XxqvRO2AMyU)

> Как это происходит?

Старая игра на DirectD3D 1-8 преобразовывается DgVoodoo в более современный DirectD3D 9/10/11 и DXVK в свою очередь переводит данные api в Vulkan

> Как узнать на какой версии DirectX игра?

[pcgamingwiki](https://www.pcgamingwiki.com/wiki/Home)

Плюсы использования связки DgVoodoo+DXVK:

- Стабильно играть в старые игры
- Можно использовать разные переменные DXVK (такие как DXVK_FRAME_RATE и т.д)
- Никаких вылетов, даже используя Alt+Tab
- Mangohud появляется, и не пропадает (необходим [lib32-mangohud](https://aur.archlinux.org/packages/lib32-mangohud))
- Запись и захват игры с помощью Obs происходит без каких-либо проблем
- В WM (Оконных менеджерах) нету никаких проблем, особенно с использованием композитора picom и переключении рабочих столов (тестировалось на i3wm)
- Отлично работает в Wayland сессии

Работает на стандартном wine-staging

```bash
sudo pacman -Sy wine-staging
```

Тестировалось на примере игры [Freedom Fighters](https://www.pcgamingwiki.com/wiki/Freedom_Fighters)

Прописываем путь до своего префикса (слепок windows) и устанавливаем игру прописывая путь к setup.exe

> В процессе установки не забываем устанавливать игру в Z:\home\username\Games

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/FF" wine путь_к_setup.exe
```

Накатываем dxvk в префикс wine

**Способ 1** - при помощи AUR пакета [dxvk-bin](https://aur.archlinux.org/packages/dxvk-bin)

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/FF" setup_dxvk install
```

**Способ 2** - используя winetricks

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/FF" winetricks -q dxvk
```

Затем накатываем dgVoodoo

Для этого копируем 4 dll'ки внутри из [скачанного dgvoodoo](http://dege.freeweb.hu/dgVoodoo2/dgVoodoo2/) **/dgVoodoo2_79_3/MS/x86/** в папку с игрой

Пояснение по данным dll файлам:

- ddraw - Содержит все реализации DirectDraw до версии 7
- d3dimm.dll - Содержит все реализации Direct3D до версии 7.
- d3d8.dll - Содержит реализацию Direct3D 8.1.
- d3d9.dll - Содержит реализацию Direct3D 9.0c.

> **(Опционально)** Также можно править вручную параметры в конфиге **dgVoodoo2.conf**.
> Или можно (используя default префикс) открыв через wine (не указывая префикс) файл dgVoodooCpl.exe и изменять там нужные для вас параметры и потом после сделанных изменений (нажав **Apply**) копируете модифицируемый **dgVoodoo2.conf** в папку игры

Перед запуском игры необходимо дать указание wine использовать нативные dll'ки (т.е которые используются от dgvoodoo внутри игры) указав данную переменную.

```bash
WINEDLLOVERRIDES="d3d8,d3d9,d3dimm,ddraw=n,b"
```

Полный запуск с терминала:

```bash
DXVK_HUD=compiler DXVK_FRAME_RATE=60 obs-gamecapture mangohud gamemoderun WINEDLLOVERRIDES="d3d8,d3d9,d3dimm,ddraw=n,b" WINEPREFIX="$HOME/.local/share/wineprefixes/FF" wine $HOME/.local/share/wineprefixes/FF/drive_c/users/Public/Desktop/Freedom\ Fighters.lnk
```

Для созданного .desktop ярлыка:

```bash
....
Exec=env DXVK_HUD=compiler DXVK_FRAME_RATE=60 obs-gamecapture mangohud gamemoderun WINEDLLOVERRIDES="d3d8,d3d9,d3dimm,ddraw=n,b" WINEPREFIX="/home/anix/.local/share/wineprefixes/FF" wine C:\\\\users\\\\Public\\\\Desktop\\\\Freedom\\ Fighters.lnk
.....
```

ГОТОВО!
