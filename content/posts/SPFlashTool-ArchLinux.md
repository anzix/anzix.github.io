+++
title = "SPFlashTool (Версия 5) на Arch Linux (софт на работу не проверен)"
date = 2023-03-02
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

При подключении телефона с процессором mediatek

```txt
...
[ +13,240631] usb 1-1.1: new high-speed USB device number 10 using ehci-pci
[  +0,078864] usb 1-1.1: New USB device found, idVendor=0e8d, idProduct=2000, bcdDevice= 1.00
[  +0,000005] usb 1-1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=0
[  +0,000001] usb 1-1.1: Product: MT65xx Preloader
[  +0,000001] usb 1-1.1: Manufacturer: MediaTek
[  +0,021773] cdc_acm 1-1.1:1.0: Zero length descriptor references
[  +0,000005] cdc_acm: probe of 1-1.1:1.0 failed with error -22
[  +0,080230] cdc_acm 1-1.1:1.1: ttyACM0: USB ACM device
[ноя18 00:29] usb 1-1.1: USB disconnect, device number 10
...
```

Добавляем своего пользователя в группу uucp, для того чтобы были права на запись
в виртуальный COM-порт

```bash
sudo usermod -aG uucp $(whoami)
```

В новой версии SPFlashTool 6 [spflashtool-bin](https://aur.archlinux.org/packages/spflashtool-bin)
не могу найти поле с "Scatter-loading File" и "Download-Agent File". Окей, прожал
комбинацию клавиш CTRL+ALT+v открылись доп-ные поля и появился "Download-Agent
File" НО "Scatter-loading File" **нету**

Для скачивания нормальной версии с наличием заливки scatter файла вот что мне
пришлось сделать

Дабы не пришлось собирать [qtwebkit](https://aur.archlinux.org/packages/qtwebkit)
(который подтягивает очень много зависимостей для сборки) просто устанавливаем
бинарник [qtwebkit-bin](https://aur.archlinux.org/packages/qtwebkit-bin) и ваше
время и нервы будут сохранены :)

![image](/images/SPFlashTool-ArchLinux/swappy-20221118-151453.png)

```bash
yay -S qtwebkit-bin
```

Далее просто собираем 5 версию бинарника [spflashtool5-bin](https://aur.archlinux.org/packages/spflashtool5-bin)
с наличием заливки scatter файла

```bash
yay -S spflashtool5-bin
```

Вуаля, это заняло мне 5 минут

![image](/images/SPFlashTool-ArchLinux/1668766134.png)

## Разные полезности

1. Как открыть Write Memory в таскбаре Window?

   Жмёте комбинацию клавиш `Ctrl + Alt + v`, после этого у вас будет открыт\
   раздел Write Memory. Плюс также будет доступен "Firmware Upgrade"
