+++
title = "Настройка композитора Picom"
date = 2023-04-15T15:52:02+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

* [Видео инструкция с объяснениями](https://www.youtube.com/watch?v=t6Klg7CvUxA&t=419s)

Он нужен для устранения тиринга для рабочего стола и для прозрачности окон (в том числе для нашего терминала Alacritty)

Установка и копирование конфиг файла
```sh
sudo pacman -S picom
mkdir ~/.config/picom
cp /etc/xdg/picom.conf ~/.config/picom
```

Автозапуск
```sh
nano ~/.xinitrc
```
Прописываю эту строчку ребутимся
```sh
picom -b --experimental-backends &
```
* `-b` Запустить в качестве фонового процесса для сеанса

## Отключаем тень (shadow) всплывающих окон, слой прозрачности
Чтобы убрать вот в firefox (пикрил) и других подобных программ

![](/images/compositor-picom/shadow-before.png)

За это отвечает параметр shadow, который в конфиге picom стоит в положении **true**

В конфиге picom
```sh
nano ~/.config/picom.conf
```
Ставим значение false
```
shadow = false;
```
Таким образом мы избавили firefox и другие программы от теней

![](/images/compositor-picom/shadow-after.png)

## Как убрать полупрозрачную полосу в MPV?

* https://bbs.archlinux.org/viewtopic.php?id=261232

![](/images/compositor-picom/white-line-on-mpv.png)

Решение
```sh
nvim ~/.config/picom/config
```

Под разделом "**Transparency / Opacity**" комментируем или убираем **frame-opacity = 0.7;**

PROFIT!!

## !Отключение прозрачности выпадающих меню приложений в Picom

Заходите в конфиг picom, опускаетесь в самый низ и комментируете эти две строки как показано на пикче

![](/images/compositor-picom/disable-transparency-dropdown-menu.png)

Если не нужна прозрачность на определенных окнах и всяких всплывающих списках то вот пример как это сделать


Если же хотите вообще отключить прозрачность на любых окнах, то просто выше закомментируйте строчку
```
inactive-opacity = 0.8;
```

## Отключаем fading (затухание между раб. столами)

```sh
nano ~/.config/picom/picom.conf
```
В категории **"Fading"** либо комментируем либо ставим значение **false**
```
#fading = true;
```

## Оптимизация Picom для полноэкранного режима в играх

* https://www.youtube.com/watch?v=K8kF-SfkaHE


При full-screen окна отключается композитор, увеличивая производительность

Открываем конфиг picom
```sh
nvim ~/.config/picom/picom.conf
```
Вставляем данные строки как показано ниже, сохраняем и всё
```
unredir-if-possible = true;
unredir-if-possible-exclude = [
        "class_g = 'looking-glass-client' && !focused"
]
```
## Picom-git версия с blur_kawase

Это git версия picom но с функцией замыливания фона

* <font color="red">Минус</font>: Данный блюр будет чуть больше потреблять ресурсов вашего ПК

Установка происходит через AUR пакет, стандартный пакет picom будет удалён
```
yay -S picom-git
```
После скачивания переходим в конфиг picom
```
nano ~/.config/picom/picom.conf
```
И рядом добавляем эти строки
```sh
blur-method = "dual_kawase";
blur-strength = 3;
```
Играйтесь со значением в "strength = [число]" и выберите свой предпочтительное размытие, сохраняем и смотрим результат

Чтобы после ребута все изменения сохранились необходимо править .xprofile (для DM) или .xinitrc (запуск с TTY)
```
nvim ~/.xinitrc
```
Меняем автозапуск на показанный ниже
```
picom -b --experimental-backends &
```
Вот как выглядит на значение 3

![](/images/compositor-picom/blur-picom.png)

