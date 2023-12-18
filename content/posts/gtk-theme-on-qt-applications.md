+++
title = "GTK 2/3 Оформление на QT"
date = 2023-03-24T00:44:34+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

> Пояснение: может вызвать некоторые графические глюки в интерфейсе, но лично у меня всё выглядит норм

Для этого качаем AUR пакет

```sh
yay -S qt5-styleplugins
```

И в .zshrc/.zshenv или .zprofile меняем переменную среду с qt5ct на gtk2

```sh
if [ -f "/usr/lib/qt/plugins/platformthemes/libqgtk2.so" ]; then
  export QT_QPA_PLATFORMTHEME="gtk2"
fi
```

В основном выглядит чётко и красиво

![image](/images/gtk-theme-on-qt-applications/gtk-on-qt.png)
