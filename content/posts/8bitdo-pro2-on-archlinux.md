+++
title = "!Геймпад 8BitDo Pro 2 на Arch Linux"
date = 2023-03-24T16:46:25+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Firmware 3.0

Чтобы геймпад появился в списке Blueman, Bluedevil (KDE) или `bluetoothctl` необходимо перевести в режим сопряжения на секунду удерживать эту кнопку. 4 индикатора должны мигать по очереди, и после этого геймпад должен появится в списке подключения к нему можно соединится.

![](/images/8bitdo-pro2-on-archlinux/button-8bitdo-pro2.jpg)

Если вы уже ранее подключались и не можете снова подключится, попробуйте удалить из списка Blueman или Bluedevil геймпад и тогда он в списке должен появится и подключится номрмально.

Данный геймпад работает хорошо со всеми типами подключения и режимами (кроме Switch, об этом позже).

## Работа в Wine

Использовался wine-tkg 6 rc5

Чтобы вызвать меню конфигурации контроллера

```sh
wine control joy.cpl
```

Режим Xinput

- [По проводу] Определяется как XBox 360, всё исправно работает
- [По Bluetooth] Определяется как XBox One, всё исправно работает

Режим Switch

- По проводу и по Bluetooth работают хорошо, триггеры нажимаются как кнопки, только ось круга квадратит

![](/images/8bitdo-pro2-on-archlinux/8bitdo-pro2-wine.png)

## Гироскоп в эмуляторах

- https://github.com/joaorb64/joycond-cemuhook/wiki

В некоторых из эмуляторов таких как dolphin (Wii), cemu (Wii U), citra (3DS), yuzu (Switch) стандартно невозможно использовать гироскоп. [Joycond-Cemuhook](https://github.com/joaorb64/joycond-cemuhook/) решает эту проблему путём использования UDP протокола

> Если у вас версия ядра Linux меньше 5.16 значит устанавливаем дополнительно [hid-nintendo-dkms](https://aur.archlinux.org/packages/hid-nintendo-dkms)
>
> ```sh
> yay -S hid-nintendo-dkms
> ```

Устанавливаем необходимые AUR пакеты [joycond-git](https://aur.archlinux.org/packages/joycond-git) и [joycond-cemuhook-git](https://aur.archlinux.org/packages/joycond-cemuhook-git)

```sh
yay -S joycond-git joycond-cemuhook-git
```

Конектим по проводу 8BitDo Pro 2 (режим Switch)

> Ранее в прошлых версиях AUR пакета cemuhook можно было его запускать от пользователя, сайчас же его необходимо запускать от рута (sudo)

Запускаем в консоли данной командой

```sh
sudo joycond-cemuhook
```

Теперь данные эмуляторы будут подхватывать геймпад вместе с рабочим гироскопом

У меня также [есть отдельный гайд по настройке геймпада для эмулятора dolphin](/posts/gyro-8bitdo-pro2-on-dolphin-wii)

## !Switch режим в Steam

В Steam сейчас пока невозможно использовать драйвер hid-nintendo которое встроили в mainline ядра Linux с версии 5.16 т.к Steam использует свой драйвер. Поэтому мой геймпад при подключении не определяется вообще.

Это известная проблема которую пока не исправили но есть [обходняки расписаные на Arch Wiki](https://wiki.archlinux.org/title/Gamepad#Nintendo_Switch_Pro_Controller_and_Joy-Cons), единственное что мне частично помогло это использовать [joycond](https://github.com/DanielOgorchock/joycond)

Устанавливаем [данный AUR пакет](https://aur.archlinux.org/packages/joycond-git)

```sh
yay -S joycond-git
```

Включаем и стартуем joycond.service

```sh
sudo systemctl enable --now joycond.service
```

После чего подключаем геймпад (по bluetooth или по проводу) с предварительно включённым режимом (S)witch

Далее переходим к обходняку который описан в Arch Wiki, чтобы Steam начал распознавать наш геймпад как Switch контроллер необходимо его перевести в виртуальный Pro контроллер (virtual Pro Controller). Без включённого joycond.service у вас не получится его активировать.

> Данный обходняк пока не имеет поддержки гироскопа для стима

Когда все 4 индикатора начнут мигать жмём обе кнопки `+` и `-` и после на геймпаде начнёт мигать только один индикатор а стим выведет уведомление что были подключены joycon'ы (да, стим будет отображать мой контроллер как joycon'ы)

{{< rawhtml >}}

<!-- Опции controls: autoplay/play/pause -->
<video width=100% controls play>
    <source src="/8bitdo-pro2-on-archlinux/VID_20230324_173613.mp4" type="video/mp4">
    Your browser does not support the video tag.
</video>
{{< /rawhtml >}}

![](/images/8bitdo-pro2-on-archlinux/Screenshot_20230319_012023.png)

![](/images/8bitdo-pro2-on-archlinux/Screenshot_20230319_001215.png)

Так же в статусе joycond.service будет добавлена информация что включён виртуальный Pro контроллер

```
$ systemctl status joycond.service
.....
мар 19 00:40:45 arch joycond[1482774]: driver_name: Nintendo Switch Pro Controller
мар 19 00:40:45 arch joycond[1482774]: MAC: E4:17:D8:CC:09:CD
мар 19 00:40:45 arch joycond[1482774]: adding epoll_subscriber: fd=5
мар 19 00:42:07 arch joycond[1482774]: Virtual procon paired
мар 19 00:42:07 arch joycond[1482774]: adding epoll_subscriber: fd=15
```

Именуется виртуальный Pro контроллер: `Nintendo Switch Virtual Pro Controller (/dev/input/js2)`

И теперь можно играть в Steam игры на режиме Switch, правда как было указано выше что метод не поддерживает гироскоп
