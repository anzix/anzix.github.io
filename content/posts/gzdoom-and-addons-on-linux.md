+++
title = "!GZDOOM и аддоны на Linux"
date = 2023-07-08T15:05:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

## Настройка GZDOOM

При запуске меню будет крупным, это проблематично при запуске Project Brutality. Вот как это исправить

> Full options menu -> Miscellaneous Options -> отключить "Disable Menu Clean Scaling"

Делаю всё пиксельным а не размытым

> Display Option - Texture Filter mode - None

Включает тень для теневых карт (реалистично)

> Full option menu - Display Option - Dynamic Light Option - включить "Light shadowmaps"

## Настройка ZDL ланучера

Как запустить главное меню DOOM а не мгновенный запуск первой карты

> Выставляете MAP поле пустым а Skill ставите на None

## Установка Project Brutality

Установка Staging Build (самая последняя версия)

Если зайти на [GitHub страницу PB](https://github.com/pa1nki113r/Project_Brutality) то там будет старая версия т.е ветка (master)\
Меняем ветку с **master** на **PB_Staging** и у вас будет самая последняя версия

Скачиваем .zip архив и просто переносим **Project_Brutality-PB_Staging.zip** в ZDL лаунчер

### Настройка Project Brutality

Убрать углы шлема PB. Заходим в **Project Brutality Settings** - **HUD Settings** и выставляем

> Helmet Corners - No\
> Vision Glass - No

Возможность прыгать в кислоте, упрощает прохождение карт\
Заходим в **Project Brutality Settings** - **Gameplay Settings** - включаем "Advanced movement on hazard floors"

## Доп аддоны Project Brutality (по порядку загрузки после PB)

1. [Glory Kill Staging](https://brutalrepository.pl/index.php/downloads/project-brutality/others/summary/5-others/7-glory-kills)\
   Позволяет настраивать казнь, её индикация, длительность и радиус сбора аптечек/брони/боеприпасов.

   > **! Примечание**: У меня с версией 993dc8g появляется баг - оружие при перезарядке не обновляет значение на обойму

   В настройках Doom появится опция **Glorykill Option**, вот моя настройка

   > Glory kill - yes\
   > Holdable Crucible - yes\
   > Stun duration - 3.0\
   > ...\
   > Collecting Health & Armor Radius - 4.0 или более

   Также включаем **HUD Glory** и настраиваем его расположение

   > Glory Hud Enable: Yes\
   > Glory Hud Style: Eternal Style\
   > Glory Hud X: -595.0\
   > Glory Hud Y: 220.0

2. [DarkDoomZ](https://github.com/caligari87/darkdoomz/releases)\
    Более тёмный DOOM + добавляет фонарик который жрёт FPS\
    Чтобы сделать фонарик менее прожорливым к fps надо выставить такие настройки

   > Quality: Fancy\
   > Incandescent (или Red Filter)\
   > Position: Helmet

3. [HitMarkers](https://github.com/Tekkish/HitMarkers/releases)\
   Настройка HitMarkers

   > Настройки Doom -> HitMarkers Option -> отключить звук меток "Use Hit Sound"

4. [Destroyable Decorations](https://brutalrepository.pl/index.php/downloads/download/5-others/80-destroyable-decorations)
5. [Damage Indicator](https://www.moddb.com/mods/qol-power-trip/addons/damage-indicator1)\
   Добавляет круговой индикатор с какой стороны прилетело пуля

### Карты для Project Brutality

1. [Extermination Day Vanila](https://www.moddb.com/mods/brutal-doom/downloads/eday-vanilla-for-vanilla-and-mods)\
   Должно быть первым в списке загружаемых .pk3 вторым идёт PB

## LiTDoom (Live Through DOOM) 0.595c

- [Офф. страница + Скачать](https://forum.zdoom.org/viewtopic.php?t=68573)

### Настройка LiTDoom

Интерфейс сделать крупнее

> Scaling Option - User interface scale = 3

Делаем темнее окружение для атмосферы

> Display Option - Sector light mode = Dark (стоит по умолчанию, вроде)

В LitDoom присутствует фонарик как из DarkDoomZ, его можно также настроить как и в PB

#### Разное полезное

[Адаптация HUD'а шлема под размер экрана](https://imgur.com/s0nivVC)

### Доп аддоны LiTDoom

1. [HitMarkers](https://github.com/Tekkish/HitMarkers/releases)

Звуки/Музыка

1. [Ambient шум](https://www.moddb.com/mods/brutal-doom/addons/alternate-dark-ambient-music-pack)\
   Из-за него может зависнуть при спавне монстров

2. [Ambient hell](https://www.doomworld.com/idgames/music/hellsamb)

### Карты для LiTDoom

1. [No Hope for Life](https://www.doomworld.com/idgames/levels/doom/Ports/m-o/nhfl)
2. [Zero Brightness](https://www.doomworld.com/forum/topic/132422-zero-brightness-10/)
