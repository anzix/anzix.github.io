+++
title = "Окрашиваем GTK тему к root приложениям"
date = 2023-03-23T23:50:37+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++
## Первый вариант
Вводим в терминале
```sh
sudo lxappearance
```
И меняем тему как обычно
## Второй вариант
Самый простой способ это создать символические ссылки

Для GTK 2.0
```sh
sudo rm -r /usr/share/gtk-2.0/gtkrc
sudo ln -s ~/.config/gtk-2.0/gtkrc-2.0 /usr/share/gtk-2.0/gtkrc
```

Для GTK 3.0
```sh
sudo rm -r /usr/share/gtk-3.0/settings.ini
sudo ln -s ~/.config/gtk-3.0/settings.ini /usr/share/gtk-3.0/settings.ini
```
* Проверено на примере редактора изображений CD, написанный в GTK2 темой [materia dark](https://archlinux.org/packages/community/any/materia-gtk-theme/)
* Проверено на примере файлового менеджера pcmanfm-gtk3 с темой [materia dark](https://archlinux.org/packages/community/any/materia-gtk-theme/)

