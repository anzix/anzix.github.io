+++
title = "!Ручное создание wine префикса для пиратской игры без использования Lutris"
date = 2023-03-04T19:21:49+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Руководствовался по этому гайду

- [Видео инструкция](https://www.youtube.com/watch?v=tbbXoqDfkY0)

В данном примере будет игра [TMNT Mutants in Manhattan](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles:_Mutants_in_Manhattan)

Указываю создание префикса для игры TMNT Mutants in Manhattan и заодно по пути указываю установщик игры

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" wine setup.exe
```

Но выдал ошибку что не хватает MFC42.DLL (Microsoft Visual C++)

## Установка dll (библиотеки) через winetricks

Метод через терминал удобен т.к все библиотеки вы можете установить без всплывающих окон и ошибок благодаря опциям

- `-q` тихая установка (никаких всплывающих окон)
- `--force` не проверять что библиотека уже была установлена, также игнорирует: SHA256 mismatch!

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

После завершения уставщика переходим на этап установке dxvk

## Установка DXVK

> Вопрос: Чем его накатывать AUR пакетом [dxvk-bin](https://aur.archlinux.org/packages/dxvk-bin) или используя `winetricks dxvk`?
>
> Вероятнее всего я бы предпочёл бы через winetricks т.к это более надёжный метод.
> Однако для тех кто юзает DXVK совместно с DgVoodoo или DxWrapper для старых игр для меня это рандом.
>
> Накатив DXVK из winetricks и используя DxWrapper я столкнулся в игре Freedom Fighters с багнутыми моделями персонажей стоящих в T-позе и с глючными текстурами (конфиг настроек dxwrapper.ini особо не менял только vsync включил)
> С DgVoodoo такого нет из накатанного [dxvk-bin](https://aur.archlinux.org/packages/dxvk-bin) и `winetricks dxvk`

Добавляю dxvk в префикс TMNTMiM

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TMNTMiM" winetricks -q dxvk
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
