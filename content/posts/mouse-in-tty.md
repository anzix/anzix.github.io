+++
title = "Мышь в tty с помощью Gpm"
date = 2023-03-09T19:58:12+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

* https://wiki.archlinux.org/title/General_purpose_mouse

Обычно пакет уже предустановлен

Всё что нужно сделать это запустить сервис в tty
```sh
sudo systemctl start gpm.service
```

И курсор сразу появится

Копирование текста выполняется простым выделением и тест будет занесён в Primary clipboard. Вставка текста - Нажатием на колёсико мыши или RMB

