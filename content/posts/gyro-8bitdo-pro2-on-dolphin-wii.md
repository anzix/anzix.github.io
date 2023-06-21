+++
title = "!Motion control гироскопом с геймпада 8BitDo Pro 2 для эмулятора Dolphin (Wii)"
date = 2023-03-09T00:22:10+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

* [Ориентировался по этому видео гайду](https://www.youtube.com/watch?v=nx5ppsALrC8) (мёртвая ссылка)
* [Dolphin-emu Wiki](https://wiki.dolphin-emu.org/index.php?title=DSU_Client#Linux)
* [Офф инструкция Joycond-cemuhook](https://github.com/joaorb64/joycond-cemuhook/wiki#Dolphin)

> Примечание: Данный метод работает только по проводу, при соединении по bluetooth [joycond-cemuhook](https://aur.archlinux.org/packages/joycond-cemuhook-git) не видит мой контроллер. Возможно это когда-нибудь исправят.

Тестировал на примере игры Super Mario Galaxy 1/2

Качаем AUR пакеты [joycond](https://aur.archlinux.org/packages/joycond-git) (своего рода Linux альтернатива BetterJoy) + дополнительно [joycond-cemuhook](https://aur.archlinux.org/packages/joycond-cemuhook-git) который позволит запустить dsu клиент (путём udp протокола) для работы гироскопа на эмуляторе.
```sh
yay -S joycond-git joycond-cemuhook-git
```

Конектим по проводу 8BitDo Pro 2 (режим Switch)

Запускаем в консоли данной командой
```sh
sudo joycond-cemuhook
```

Если видите такой выхлоп значит 8BitDo Pro 2 определился и запустил сервис

![](/images/gyro-8bitdo-pro2-on-dolphin-wii/Screenshot_20230315_170415.png)

Оставляем терминал открытым, либо запустите как команду как процесс `sudo joycond-cemuhook &`

Заходим в эмуль dolphin - Опции - Настройки управления. В окне категории Общее ставим галочку на "Ввод в фоне" (без этого у меня не работали кнопки управления и motion control) и следом жмём на "Другие источники ввода". Включаем клиент DSU потом жмём "Добавить" и в "Описание" можно любое (например название нашего геймпада)

![](/images/gyro-8bitdo-pro2-on-dolphin-wii/Gyro-Motion-Control-Dolphin-Wii-Mario-Galaxy-2.png)

После добавления мы можем убедится что Motion control работает жмём на "Настройка" рядом с `Wii Remote 1 [Эмулируемый Wii Remote]`

Выбираем из списка наш добавленный DSU клиент, у меня он именуется так `DSUClient/0/8BitDo Pro 2`

**Данные движения** (motion) т.е гироскоп:

Если у вас пустые поля в секции Акселерометр и Гироскоп сбрасываем настройки нажав "По умолчанию"

В этой категории убеждаемся что всё дефолтно работает (при движении и вращении геймпада оба кружка должны двигаться)

![](/images/gyro-8bitdo-pro2-on-dolphin-wii/Gyro-Motion-Control-Dolphin-Wii-Mario-Galaxy.gif)

[Центровка] Правое нажатие на стик - R3 (Центровка motion курсора)

**Общие и настройки**:

Необходимо чтобы кнопки отображались при нажатии, как на гифке

![](/images/gyro-8bitdo-pro2-on-dolphin-wii/Gyro-Motion-Control-Dolphin-Wii-Mario-Galaxy-1.gif)

Также не рекомендуется включать Вибрацию с включенным Motion управлением, курсор при геймплее будет постоянно трястись и будет сильно мешать при геймплее

**Расширение (управление персонажем)**:

Обязательно откалибруйте стик

![](/images/gyro-8bitdo-pro2-on-dolphin-wii/stick-calibration.png)

* [C] Правый trigger (курок) - R1 (марио приседает)
* [Z] Правый shoulders (бампер) - R2 (корректировка камеры)

Теперь можно спокойно играть

