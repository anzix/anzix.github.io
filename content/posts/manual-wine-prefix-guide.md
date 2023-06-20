+++
title = "!Ручное создание wine префикса для пиратской игры без использования Lutris"
date = 2023-03-04T19:21:49+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++

Руководствовался по этому гайду
* https://www.youtube.com/watch?v=tbbXoqDfkY0

В данном примере будет игра [TMNT Mutants in Manhattan](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles:_Mutants_in_Manhattan)

Указываю создание префикса для игры TMNT Mutants in Manhattan и заодно по пути указываю установщик игры
```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" wine setup.exe
```
Но выдал ошибку что не хватает MFC42.DLL (Microsoft Visual C++)

## Установка dll (библиотеки) через winetricks
Метод через терминал удобен т.к все библиотеки вы можете установить без всплывающих окон и ошибок благодаря опциям
* `-q` тихая установка (никаких всплывающих окон)
* `--force` не проверять что библиотека уже была установлена, также игнорирует: SHA256 mismatch!
```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" winetricks -q --force mfc42 vcrun2013 vcrun2015
```
Теперь можно спокойно установить игру
```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" wine setup.exe
```
После чего установка появится и можно устанавливать игру

В установщике убираю галочки об установке дополнений (зависимостей) dll т.к они уже есть

Путь установленной игры у меня это ~/Games т.е в установщике это диск Z:\home\[username]\Games\

После завершения установки скачиваем из AUR [бинарный пакет dxvk](https://aur.archlinux.org/packages/dxvk-bin)
```bash
yay -S dxvk-bin
```

Далее добавляю dxvk в префикс TMNTMiM
```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" setup_dxvk install
```
После добавления можно запустить игру по созданному ярлыку
> В ярлык игры можно добавить переменные для улучшения производительности и другие вспомогательные штуки
```bash
DXVK_HUD=compiler vk_radv obs-gamecapture gamemoderun mangohud
```
Игра работает замечательно, даже с геймпадом 8BitDo Pro 2 (Xinput по проводу)

## Как удалить игру или программу?

Для конкретного префикса вводим
```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" wine uninstaller
```
После чего удаляем игру

Для простого удаления префикса, чтобы не занимал места
```bash
rm -rf ~/.local/share/wineprefixes/TMNTMiM
```
