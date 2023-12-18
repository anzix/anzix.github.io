+++
title = "!Конвертируем Debian пакет в совместимый для Arch Linux пакет"
date = 2023-07-14T19:40:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Очень полезно если данных пакетов нету ни в официальных репозиториях Arch Linux ни в AUR\

На данном примере я буду показывать пакет [XPQ4](https://xpq4.sourceforge.io/) который предназначен для Q4OS дистрибутива основанный на Debian и Ubuntu\
Он меняет внешний вид для [рабочего окружения Trinity](https://www.trinitydesktop.org/) симулируя Windows 95/XP/7/8/10 вместе с иконками

Скачиваем с репозитория .deb пакеты

- Основной пакет [xpq4](https://sourceforge.net/projects/xpq4/files/debrepo/pool/main/x/xpq4/)
- Зависимость [luna](https://sourceforge.net/projects/xpq4/files/debrepo/pool/main/l/luna/)
- Зависимость [icontheme-winten](https://sourceforge.net/projects/xpq4/files/debrepo/pool/main/i/icontheme-winten/)

Скачиваем с AUR конвертер .deb пакетов в пакеты Arch Linux

```sh
yay -S debtap
```

Обновляем базу данных

```sh
sudo detap -u
```

И конвертируем по порядку

```sh
debtap xpq4_4.4-a1_all.deb
debtap luna_1.1.1-a1_amd64.deb
debtap icontheme-winten_3.2-a1_all.deb
```

> После каждого пакета вам могут предложить изменить некоторые строки в `.PKGINFO` если debtap обнаружил несовместимость или ошибку при конвертации

После проделанных изменений вы можете уже устанавливать конвертированные пакеты командой

```sh
sudo pacman -U icontheme-winten-3.2.a1-1-any.pkg.tar.zst luna-1.1.1.a1-1-x86_64.pkg.tar.zst xpq4-4.4.a1-1-any.pkg.tar.zst
```

И вот результат, однако есть вероятность не полной работоспособности некоторых исполняемых файлов который поставляет конвертированный .deb пакет

![image](/images/debpkg-to-archpkg/снимок2.png)
