+++
title = "OpenTabletDriver для XP-Pen G640 [Rev A] на Arch Linux"
date = 2023-03-10T17:40:37+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

- [GitHub страница](https://github.com/OpenTabletDriver/OpenTabletDriver)

Скачиваем [AUR пакет](https://aur.archlinux.org/packages/opentabletdriver) (обычная версия, не git) и пакет xorg-xinput чтобы курсор граф. планшета двигался

```sh
yay -S xorg-xinput opentabletdriver
```

После установки пакета всплывёт понятная инструкция, следуем ей или за моей инструкцией

Для автозапуска прописываем в файл `~/.xprofile` (Для DM) или `~/.xinitrc` (запуск с TTY)

```sh
otd &
```

Для тех кто использует Дисплей менеджеры

```sh
systemctl --user enable opentabletdriver.service
```

Запустите GUI opentabletdriver

Во вкладке Console у меня не определялся G640 т.к была ошибка <span style="color:red">Another tablet driver found: UC Logic</span> <span style="color:green">решением</span> было удалить встроенный модуль драйвера в ядре Linux [командой rmmod](https://www.opennet.ru/man.shtml?topic=rmmod&category=8&russian=2) и добавив его в blacklist modprobe чтобы больше не запускался.

Выхлоп консоли otd

```txt
[Detect:Warning] Another tablet driver found: UC Logic
[Detect:Warning] Detection for UC Logic tablets might be impaired
```

Вводим эти команды для <span style="color:green">решения</span> проблемы

```sh
echo "blacklist hid_uclogic" | sudo tee -a /etc/modprobe.d/blacklist.conf >/dev/null
sudo rmmod hid_uclogic
sudo mkinitcpio -P
```

Перезапускаем ПК

OpenTabletDriver быстро определит граф. планшет и начнёт работать

Теперь в консоли у меня только info output. Никаких ошибок и планшет работает как надо

![](/images/OpenTabletDriver-xp-pen-g640-rev-a-on-archlinux/2022-03-18-22-19.png)

## Возвращаем режим рисования с функцией давления на перо

Открыв OpenTabletDriver меняем Absolute Mode на Artist Mode

## При нажатии на кнопки курсор пера дрожит и нет сглаживания курсора

Для этого нам поможет фильтр **Devocub Antichatter**. Идём **Filters** жмём **Open Plugin Manager**, из списка выбираем **DevocubFilters** и жмём **Install**

После установки переходим в Filters и видим что наш фильтр установлен. Для устранения проблем просто ставим чекбокс Enable Devocub Antichatter и жмем Apply

Проблема решена!

> Хочу подчеркнуть то что включённый Antichatter начинает отжирать больше процессорных ресурсов, в htop один любой из потоков под 100% нагрузкой. Включайте его при необходимости а не на постоянку
>
> UPD [20.07.22] С обновлением OpenTabletDriver v0.6.0.4 Antichatter теперь отжирание процессорных ресурсов ресурсов CPU составляет 1%
