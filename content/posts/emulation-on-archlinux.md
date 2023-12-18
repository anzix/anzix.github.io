+++
title = "Эмуляция на Arch Linux"
date = 2023-03-25T17:17:32+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

## Playstation 1 (Duckstation) На Arch Linux

- [Github страница Duckstation](https://github.com/stenzek/duckstation)
- [Видео гайд по настройке для Linux](https://www.youtube.com/watch?v=qOVhug5-mNM)

Устанавливаем AUR пакет [duckstation-git](https://aur.archlinux.org/packages/duckstation-git/)

```sh
yay -S duckstation-git
```

Процесс компиляции займёт немного времени и ресурсов ПК

- [По проводу] Геймпад 8BitDo Pro 2 хорошо себя ведёт, без нареканий
- [По Bluetooth] Геймпад тоже очень хорошо себя ведёт

Не забудьте скачать bios'ы для эмулятора

## !Playstation 2 (PCSX2) На Arch Linux

- [Официальный сайт PCSX2](https://pcsx2.net/)
- [Видео гайд по настройке для Linux](https://www.youtube.com/watch?v=7Q0gy0s8Mxo)

Установка стабильной версии (проверял на этой версии)

```sh
sudo pacman -S pcsx2
```

Если хотите более экспериментальную и более новую версию данного эмулятора, качаем AUR пакет [pcsx2-git](https://aur.archlinux.org/packages/pcsx2-git/), обнова каждый день выходит

```sh
yay -S pcsx2-git
```

Запускаем и настраиваем

- [По проводу] Мой геймпад 8BitDo Pro 2 по проводу (в режиме xinput) сразу определился в эмуляторе с поддержкой вибрации.

> Есть небольшая проблема, вибрация на геймпаде постоянно интенсивная, нету перехода из слабого моторчика в сильный при геймплее. Проблема не в геймпаде, и не в настройке пада на Arch’е, дело в самом эмуляторе pcsx2.
> Решил я эту проблему установив AUR пакет [pcsx2-git](https://aur.archlinux.org/packages/pcsx2-git/)
>
> ```sh
> yay -S pcsx2-git
> ```

- [По Bluetooth] Геймпад в режиме (S)witch и (X)input определяется и работает, правда вот из-за прошивки 1.03 у геймпада траблы с вибрацией (проверял на Git версии), но в целом всё работает

Биосы и iso’шники берите и ищите сами, думаю это не составит труда

## Nintendo 3DS (Citra) На Arch Linux

Качаем данный [AUR пакет](https://aur.archlinux.org/packages/citra-qt-git)

```sh
yay -S citra-qt-git
```

Запускаем ярлык citra

- [По проводу] Мой геймпад по проводу при игре не определялся автоматический
  > Чтобы он определился нужно перейти в "Emulation" - "Configure" во вкладке "Controls" жмём "Auto Map" после нажатия "OK" нужно нажать любую клавишу на геймпаде. И тогда геймпад 8BitDo Pro 2 сразу заработал
- [По Bluetooth] Геймпад хорошо работает, также необходимо нажать автоназначение. В целом всё работает.

### Настройка Citra для 2-х мониторов на Linux

Гайд был написан с Арчом в окружении KDE Plasma, но я думаю что и на тайлинг подойдёт

![image](/images/emulation-on-archlinux/IMG_20220618_211811.jpg)

- [Инструкция 1](https://community.citra-emu.org/t/guide-dual-monitor-setup-noob-friendly/539526) (для юзеров Windows, но и для Linux идёт)
- [Инструкция 2](https://community.citra-emu.org/t/displaying-the-two-citra-screens-on-two-different-monitors/11199) (почти тоже самое что и первое)
- [Видео инструкция](https://www.youtube.com/watch?v=5QBGU56xNdQ) (юзать смартфон как 2-й дисплей, но этапы данной инструкции те же)

При подключении второго дисплея (который будет использоваться как нижний дисплей 3ds) разместил его справа от основного

![image](/images/emulation-on-archlinux/citra-monitor-layout.png)

Закрываем citra и открываем конфиг

```sh
nvim ~/.config/citra-emu/qt-config.ini
```

В нём меняем:

- `layout_option=0` измените его на `1`
- `custom_layout=false` измените его на `true`
- Измените все `custom_xxx_xxx\default=true` на `false`

Пример

```txt
custom_bottom_bottom\default=false
custom_bottom_left\default=false
custom_bottom_right\default=false
custom_bottom_top\default=false
custom_layout\default=false
custom_top_bottom\default=false
custom_top_left\default=false
custom_top_right\default=false
custom_top_top\default=false
```

[Качаем данный файл](/emulation-on-archlinux/main.htm) и открываем через ваш браузер

![image](/images/emulation-on-archlinux/calc-secondary-monitor-for-citra.png)

Указываем разрешение основного и второго монитора, и данные значения из строк вставляем в `qt-config.ini` и сохраняем

У меня вышло так

![image](/images/emulation-on-archlinux/my-layout-for-monitor.png)

Запускаем Citra

В тулбаре Citra вкладка **View** убираем чекбокс **Single Window Mode**

Настройка закончена можно запускать

> Насчёт того как расположить второй дисплей на лево отредактировав конфиг ещё не допёр. Но в целом результатом доволен

## !Nintendo GameCube (Dolphin) На Arch Linux

- [Видео гайд по настройке для Linux](https://www.youtube.com/watch?v=sHyKA5OrRdQ)

Качаете обычную стабильную версию

```sh
sudo pacman -S dolphin-emu
```

Либо экспериментальную версию git

```sh
yay -S dolphin-emu-git
```

На первом запуске эмуля необходимо вручную настроить управление и вибрацию геймпада

Игры GameCube:

- [По проводу] 8BitDo PRO 2 в режиме Xinput работает хорошо
- [По Bluetooth] В режиме Xinput (показывается как evdev/0/8BitDo Pro 2) идеально всё работает без проблем даже вибрация (настройка вибрации такая: Sine|Square|Triangle|Strong|Weak)

Игры на Wii Нунчак Motion controls т.е гироскопом:

- [По проводу] Работает также как и по bluetooth
- [По Bluetooth] Работает замечательно (выключена вибрация) с помощью DSU сервера + joycond-cemuhook, сделал гайд по настройке

Производительность на пресете vulkan (на моей RX580 4GB) в разы лучше и стабильнее фпс

### Включаем заставку GameCube

[Скачиваем из google drive](https://drive.google.com/drive/folders/0B3FTVdj1y-LYcVdwZzloSl83WVk?resourcekey=0-IneDlrReX1_1cYO9cmiFgg) только один дамп ipl gamecube это USA

Лучше всего работает биос (заставка GC) от USA

Разместить скачанный USA дамп в `~/.local/share/dolphin-emu/GC/USA`. В настройках Dolphin во вкладке "GameCube" отключить "Пропускать главное меню"

Если видите ошибку `"The IPL File Is Not a known good dump"`. Отключите в настройках "Использовать обработчики ошибок"

Done!

### Эмуляция Nunchuck для геймпада Xbox

В данном гайде для примера будет выступать bluetooth 8BitDo Pro 2 Xinput режим, настройка которая работает замечательно на Arch Linux'е так и на Шинде

[Настраивал по этому видео гайду](https://www.youtube.com/watch?v=ID1rDKiaLsE)

### Эмуляция Nunchuck с помощью гироскопа 8BitDo Pro 2

По проводу в режиме Switch и используя joycond-cemuhook мне удалось использовать гироскоп как Нунчак

[Мой вручную написанный гайд по настройке](/posts/gyro-8bitdo-pro2-on-dolphin-wii), идеально работает прям как на Шинде с использованием BetterJoy

## !!XBox (Xemu)

- [Видео гайд по настройке для Linux](https://www.youtube.com/watch?v=-1EsLpJS90g)

## !Nintendo WiiU (Cemu)

Так как Cemu теперь open source мы будем использовать именно её

```sh
yay -S cemu
```

В время запуска игры у меня звук не появлялся, <font color="green">решением</font> было зайти в Основные настройки (General settings) - вкладка "Audio" - и в секции TV выбрать ваше выходное аудио устройство (Device)

Настройки геймпада (Input Settings)

Обязательно выбираем Wii U GamePad и назначаем кнопки

Геймпад 8BitDo Pro 2 (прошивка 3.0) в режиме Switch по проводу и по Bluetooth не работает гироскоп без UDP подключения т.е необходим [joycond-cemuhook](https://github.com/joaorb64/joycond-cemuhook)

### !!Оптимизация Cemu

[Коллекция кэшей шейдеров для Cemu](https://chriztr.github.io/cemu_shader_and_pipeline_caches/)

Закидываем кэш шейдеров из любой игры в данный путь
$HOME/.local/share/cemu/

## (Proprietary) Sega DreamCast (Redream) На Arch Linux

Установка данного эмулятора довольно необычная, т.к нам ничего компилировать не нужно. Достаточно просто скачать исполняемый файл и запустить его

[Скачиваем](https://redream.io/download) для Linux “Development Releases” redream, не “Stable Release” потому что дата последних обновлений на этой ветке не было аж 2 года

Распаковываем архив и запускаем командой `./redream`

## !Sony Playstation 3 (RPCS3) На Arch Linux

[Видео гайд по настройке для Linux](https://www.youtube.com/watch?v=VBE_IOCdaCQ)

> Рекомендую собрать бинарник а не целый исходный код rpcs3

Скачиваем [AUR пакет](https://aur.archlinux.org/packages/rpcs3-bin)

```sh
yay -S rpcs3-bin
```

После запуска эмуля необходимо [скачать](https://www.playstation.com/ru-ru/support/hardware/ps3/system-software/) и установить прошивку ps3

Жмём “File” - “Install Firmware” - указываем файл прошивки

Игры сами знаете где качать

- [По проводу] Обработчик Evdev, режим Xinput Геймпад 8BitDo Pro 2 по проводу прекрасно работает даже вибрация, определяется как Xbox 360
- [По Bluetooth] Работает но вибрация слабая (возможно из-за прошивки 1.03, и то что я его до сих пор не обновляю) и почему-то не все кнопки работают (например R2)

> UPD 06.05.2022 [По Bluetooth]: Версия RPCS3 0.0.22-13535 обработчик Evdev, 8Bitdo Pro 2 Firmware 1.08, режим S и A не определяется эмулятором. Определился с режимом D (Android) НО не работает нормально стики, разметка не помогает. В общем, пока не юзабельно

Настройки которые я включил в самом эмуляторе:

1. “CPU” - “Enable SPU loop detection”
2. “GPU” - “White Color Buffers” (устраняет проблемы текстур клинков Хаоса Кратоса в GOW: Collection) “Asynchronous Texture Streaming” + Увеличил разрешение на 1920x1080
3. "Audio" - "Enable Time Stretching" "75%" (устраняет ежесекундные затупы звука) просто поставил галочку и ничего не трогал

## !Nintendo Switch (Yuzu) На Arch Linux

Скачиваем AUR пакет раннего доступа yuzu (для более свежих обновлений)

```sh
yay -S yuzu-early-access
```

> Для быстрой компиляции необходимо настроить makepkg.conf

После запуска вас попросят о наличии prod.keys ключей, найти эти ключи очень легко если вы умеете гуглить. Если нашли ключи тогда кидаем ключи в эту директорию `~/.local/share/yuzu/keys` и перезапускаем yuzu

Игры тоже вы можете найти, если в интернете не первый год :)

Не забывайте добавлять обновления и DLC к игре, сделать это можно так “Файл” - “Установить файлы в NAND” - выбираете nsp файлы

- [По проводу] Геймпад 8BitDo Pro 2 отлично работает в режиме S (Switch) и X (Xinput) с вибрацией. В режиме S (Switch) сразу из коробки работает гироскоп и вибрация, ничего настраивать не нужно.
- [По Bluetooth + cemuhook] Чтобы всё заработало необходимо включить в разделе "Расширенные" пункт "Включить UDP контроллеры". И Геймпад в режиме S (Switch) будет работать замечательно прямо также как и по проводу, вибрация и гироскоп работают
