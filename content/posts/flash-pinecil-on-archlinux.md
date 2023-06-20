+++
title = "!Dfu-util - установка & обновление прошивки Ralim на купленный с Китая Pinecil"
date = 2023-03-04T20:49:54+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux", "Pinecil"]
+++

Источники:

- [Офф инструкция установки RalimOS (Linux)](https://ralim.github.io/IronOS/Flashing/Pinecil%20V1/) Ориентировался по ней
- [Сурс по установке через dfu-util](https://gist.github.com/Dids/2aa950d075bcf58fca98eb975a42d72b)

![](/images/flash-pinecil-on-archlinux/old-firmware.png)

Скачиваем dfu-util

```bash
sudo pacman -S dfu-util
```

Мануал: `man dfu-util`

## Процесс установки прошивки через dfu-util

Скачиваем [последнюю версию RalimOS](https://github.com/Ralim/IronOS/releases), в данный момент версия 2.20

Выбираем `Pinecil.zip`, распаковываем и ищем `Pinecil_RU.dfu` он нам и нужен

Включаем режим DFU на Pinecil

Для этого зажимаем кнопку - (минус) и подключаем Pinecil в ПК (дисплей на программаторе будет тёмным)

Для проверки вводим

```bash
dfu-util -l
```

```txt
....
Found DFU: [28e9:0189] ver=0100, devnum=43, cfg=1, intf=0, path="1-1.1", alt=1, name="@Option Bytes  /0x1FFFF800/01*016Be", serial="3TBJ"
Found DFU: [28e9:0189] ver=0100, devnum=43, cfg=1, intf=0, path="1-1.1", alt=0, name="@Internal Flash  /0x08000000/128*001Kg", serial="3TBJ"
```

Если у вас также как и у меня есть два DFU девайса, значит необходимо указать какой именно необходимо прошить а именно **_Internal Flash_** с цифровым указанием **_alt=0_**

Из мануала dfu-util:

```txt
-a, --alt ALT
        Specify  the  altsetting of the DFU interface by name or by num‐
        ber.
```

Перевод: `-a` **Указывает альтернативную настройку интерфейса DFU по имени или по номеру**

Внутри распакованной папки с локализациями Pinecil открываем терминале и вводим

```bash
dfu-util -D Pinecil_RU.dfu -a 0
```

Весь выхлоп после команды

```txt
dfu-util 0.11
....
Match vendor ID from file: 28e9
Match product ID from file: 0189
Multiple alternate interfaces for DfuSe file
Opening DFU capable USB device...
Device ID 28e9:0189
Device DFU version 011a
Claiming USB DFU Interface...
Setting Alternate Interface #0 ...
Determining device status...
DFU state(2) = dfuIDLE, status(0) = No error condition is present
DFU mode device DFU version 011a
Device returned transfer size 2048
DfuSe interface name: "Internal Flash  "
Found GD32VF103, which reports a bad page size and count for its internal memory.
Fixed layout based on part number: page size 1024, count 128.
File contains 1 DFU images
Parsing DFU image 1
Target name: ST...
Image for alternate setting 0, (1 elements, total size = 54300)
Setting Alternate Interface #0 ...
Parsing element 1, address = 0x08000000, size = 54292
Erase   	[=========================] 100%        54292 bytes
Erase    done.
Download	[=========================] 100%        54292 bytes
Download done.
Done parsing DfuSe file
```

После завершения отключаем от ПК и подключаем в банку проверяя работоспособность

![](/images/flash-pinecil-on-archlinux/new-firmware.png)

Как видим прошивка обновилась до 2.19 и работает замечательно

## (Не проверял) Альтернативный процесс установки/обновления прошивки через [старую версию PINE64 Updater](https://aur.archlinux.org/packages/pinecil-firmware-updater-git)

![](/images/flash-pinecil-on-archlinux/Old-PINE64-Updater.png)

Это старая QT версия ранее разработанная для Linux

```bash
yay -S pinecil-firmware-updater-git
```
