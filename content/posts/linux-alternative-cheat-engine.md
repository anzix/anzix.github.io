+++
title = "Linux (Не)альтернативы Cheat Engine"
date = 2023-03-04T18:13:01+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++
# Game Conqueror
* [Github](https://github.com/scanmem/scanmem/tree/main/gui)
* [Видео инструкция по использованию](https://www.youtube.com/watch?v=xBGCjtdfaq0)

Минусы:
* Не умеет подсвечивать изменённое значение

Установка
```bash
sudo pacman -S gameconqueror
```
Использование на примере Sims 2 Сезон Погоды (Wine Staging + DXVK 2.0)

Запускаем Game Conqueror

Нажав на иконку добавляем процесс игры, у меня это..
```
2525436 - Z:\home\anix\Games\The Sims 2 - Seasons\sims2seasons\TSBin\Sims2EP5.exe
```

После запуска игры я например хочу больше денег, чтобы найти данное значение необходимо запомнить сколько у вас денег, вводим в секции Value: значение вашего баланса в игре

Например у меня 1141, и сканируем нажав на лупу. Я получил 469 адресов

Затем начнём поиск нашего кошелька вычтем из нашего баланса деньги купив что-нибудь мелкое. После покупки ищем в Game Conqueror поле значение баланса после покупки у меня это 1129 (-12)

Я получил 2 адреса с данным значением, добавляем оба и чек-лист нажав по ним два раза

![](/images/linux-alternative-cheat-engine/1671552591.png)
![](/images/linux-alternative-cheat-engine/1671552562.png)

Чтобы узнать какой из них отвечает за денежный баланс в игре пробуйте на обеих адреса добавить значение 50000

После добавления в адрес 154d53d4 в Sims 2 я продаю предмет и значения увеличились  на то которое я выставлял
![](/images/linux-alternative-cheat-engine/changed-value.png)

# PINCE
* [Github](https://github.com/korcankaraokcu/PINCE)
* [Обзор и использование PINCE](https://www.youtube.com/watch?v=hUPvk2ejYTk)

Плюсы:
* Умеет подсвечивать изменённое значение

Есть доступный AUR пакет [``pince-git``](https://aur.archlinux.org/packages/pince-git) для скачивания, хотя PKGBUILD очень долгое время не обновлялся и именно поэтому я вам рекомендую собрать его самому

[Инструкция по сборке Pince](https://github.com/korcankaraokcu/PINCE#installing)

Для запуска в терминале вводим:
```bash
# Для AUR пакета pince-git
pince

# Для тех кто собрирал вручную
sh PINCE.sh
```
Проверял на игре Sims 2 Сезон Погоды (Wine Staging + DXVK 2.0)

Жмем на иконку компьютера с подписью "Create or attach to process" и в поиске ищем название процесса игры и жмем "Open"

Далее выбираем "Decimal" и вводим денежный баланс в игре и жмём First Scan
Затем в игре уменьшаем или увеличиваем денежный баланс, в окне адресов показано красным что значение изменилось.

![](/images/linux-alternative-cheat-engine/1671555558.png)

Жмём по адресу дважды для добавления в таблицу адресов и уже в нём меняем значение какое пожелаете, я выбрал 900000

В игре после этого надо обновить денежный счётчик и тогда денежный баланс обновится на тот который вы указали в выбранном адресе

![](/images/linux-alternative-cheat-engine/1671556150.png)

