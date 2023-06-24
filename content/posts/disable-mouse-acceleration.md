+++
title = "Ручное отключение акселерации мыши (Xorg)"
date = 2023-03-21T20:11:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

## Создав конфиг файл

Скачиваем данный пакет (если вдруг у вас его нету)

```sh
sudo pacman -S libinput
```

Далее создаём файл в этом пути

```sh
nvim /etc/X11/xorg.conf.d/50-mouse-acceleration.conf
```

И вставляем это всё, сохраняем и перезагружаемся или выходим из сессии

```
Section "InputClass"
 	Identifier "Logitech G102 Prodigy"
 	Driver "libinput"
 	MatchIsPointer "yes"
 	Option "AccelProfile" "Flat"
 	Option "AccelSpeed" "0"
EndSection
```

При ребуте у вас мышка будет правильно работать, как из винды

## Используя xset

Прописываем в `.xprofile` (для DM) или `.xinitrc` (запуск с TTY)

```sh
xset m 0 0 &
```
