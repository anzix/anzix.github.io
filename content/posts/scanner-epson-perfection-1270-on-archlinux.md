+++
title = "Сканер Epson Perfection 1270 на Arch Linux"
date = 2023-03-04T22:30:20+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

При подключении сканера:

Вывод в dmesg

```txt
....
[дек23 20:40] usb 1-1.1: new high-speed USB device number 3 using ehci-pci
[  +0,081864] usb 1-1.1: New USB device found, idVendor=04b8, idProduct=0120, bcdDevice= 1.10
[  +0,000010] usb 1-1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=0
[  +0,000005] usb 1-1.1: Product: EPSON Scanner
[  +0,000004] usb 1-1.1: Manufacturer: EPSON
....
```

```bash
lsusb
```

```txt
....
Bus 001 Device 003: ID 04b8:0120 Seiko Epson Corp. GT-7400U [Perfection 1270]
...
```

```bash
sane-find-scanner
```

```txt
....
found USB scanner (vendor=0x04b8 [EPSON], product=0x0120 [EPSON Scanner]) at libusb:001:003
....
  # Your USB scanner was (probably) detected. It may or may not be supported by
  # SANE. Try scanimage -L and read the backend's manpage.

  # Not checking for parallel port scanners.

  # Most Scanners connected to the parallel port or other proprietary ports
  # can't be detected by this program.

  # You may want to run this program as root to find all devices. Once you
  # found the scanner devices, be sure to adjust access permissions as
  # necessary.
```

Присваиваем группу scanner для доступа к sane non-root пользователю, также присваиваем группу lp для избежания проблем с вводом/выводом и пропаданием сканера (лично я сталкивался с этим)

```bash
sudo usermod -aG scanner,lp $(whoami)
```

Устанавливаем пакеты

```bash
sudo pacman -S sane sane-airscan ipp-usb xsane
```

- sane - основной пакет для сканеров
- sane-airscan (на всякий) - для сканеров которые работают в режиме «без драйверов»
- ipp-usb - для сканеров которые работают по USB
- xsane - Функциональное ПО для выполнения сканирования документов

Разрешаем использование протокола IPP через соединение USB включив данный сервис

```bash
sudo systemctl enable --now ipp-usb.service
```

Однако данный сканер не работает "из коробки" об этом свидетельствует выхлоп из команды `scanimage -L`, который был пуст

<span style="color:green">РЕШЕНИЕ</span>: Из [Arch Wiki (Scanner-specific problem)](https://wiki.archlinux.org/title/SANE/Scanner-specific_problems#Epson_Perfection_1270), [Rosa Wiki (Настройка для работы сканера Epson Perfection 1270)](http://wiki.rosalab.ru/ru/index.php/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0_%D0%B4%D0%BB%D1%8F_%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D1%8B_%D1%81%D0%BA%D0%B0%D0%BD%D0%B5%D1%80%D0%B0_Epson_Perfection_1270) и на [офф сайте Sane в списке поддерживаемых устройств (Current Stable SANE Version)](http://www.sane-project.org/sane-backends#:~:text=Requires%20firmware%20esfw3e.bin.%0Aoverseas%20version%20of%20the%20GT%2D7400U) указано что что для данного сканера необходима прошивка Esfw3e.bin взятый из:

1. Из распакованных драйверов [Epson](https://epson.com/Support/Scanners/Perfection-Series/Epson-Perfection-1270/s/SPT_B11B166081), а именно разархивировав **ModUsd.cab** при помощи `cabextract` внутри которого будет данный файл прошивки
2. Вытащить данный файл из установленного Windows раздела находящийся по пути C:\Windows\System32

Вытащив данный файл прошивки я копирую его в данный путь

```bash
sudo cp Esfw3e.bin /usr/share/sane/snapscan
```

После чего редактирую данный конфиг файл и меняю в `firmware [путь до файла Esfw3e.bin]`

```bash
sudo -e /etc/sane.d/snapscan.conf
```

```bash
....
firmware /usr/share/sane/snapscan/Esfw3e.bin
....
```

Там же в самом низу добавляем

```bash
...
# Epson Perfection 1270
usb 0x04b8 0x0120
...
```

> Примечание: информацию о коде (usb 0x04b8 0x0120) можно получить с помощью команды `sane-find-scanner`

Также добавляем данные строки чтобы настроить свои привилегии

```bash
sudo mkdir -p /etc/hotplug/usb/
sudo -e /etc/hotplug/usb/libsane.usermap
```

```bash
# Epson Perfection 1270
libusbscanner 0x0003 0x04b8 0x0120 0x0000 0x0000 0x00 0x00 0x00 0x00 0x00 0x00 0x00000000
```

После переподключения сканера при выполнении сканирования используя команду `scanimage -L` определился

```bash
...
device snapscan:libusb:001:007 is a EPSON EPSON Scanner flatbed scanner
...
```

> Примечание: Чтобы сканер работал правильно необходимо отключать калибровку качества, с калибровкой при сканировании появляются неправильные цвета. Ниже приведены примеры как отключить данную калибровку.

`scanimage` (сканирование через терминал):

- добавляем опцию `--quality-cal=no`

Пример сканирования через терминал:

```bash
scanimage --device snapscan --quality-cal=no --resolution 300 --format=png --output-file test.png --progress
```

xsane:

- в окне "Стандартные параметры" снять галочку "Калибровка качества"

# Проблема

В текущий момент сканер работает стабильно, однако на отсканированном справа появляется толстый белый горизонтальный прямоугольник с разноцветными линиями внутри. Это происходит на `scanimage` и xsane. С [данной проблемой сталкиваюсь не один я](https://eugeneap.livejournal.com/3630.html#:~:text=%D0%9E%D1%81%D1%82%D0%B0%D0%BB%D0%B0%D1%81%D1%8C%20%D0%BF%D1%80%D0%BE%D0%B1%D0%BB%D0%B5%D0%BC%D0%B0%20%2D%20%D1%80%D0%B0%D0%B7%D0%BD%D0%BE%D1%86%D0%B2%D0%B5%D1%82%D0%BD%D0%B0%D1%8F%20%D0%BF%D0%BE%D0%BB%D0%BE%D1%81%D0%B0%20%D1%81%D0%BF%D1%80%D0%B0%D0%B2%D0%B0%20%D0%BF%D1%80%D0%B8%20%D1%81%D0%BA%D0%B0%D0%BD%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B8%20%D1%88%D0%B8%D1%80%D0%B8%D0%BD%D0%BE%D0%B9%20%D0%BF%D1%80%D0%B8%D0%BC%D0%B5%D1%80%D0%BD%D0%BE%20%D1%82%D1%80%D0%B5%D1%82%D1%8C%20%D0%BB%D0%B8%D1%81%D1%82%D0%B0.%20%D0%9A%D0%B0%D0%BA%20%D0%B1%D0%BE%D1%80%D0%BE%D1%82%D1%8C%20%2D%20%D1%84%D0%B8%D0%B3%20%D0%B5%D0%B3%D0%BE%20%D0%B7%D0%BD%D0%B0%D0%B5%D1%82). Полноценного решения как это исправить я пока не нашёл, только временные решения.

<span style="color:green">Временные решения</span>:

Для xsane: обойти это можно просто выделить участок на предварительном сканировании и на отсканированном файле будет без линий.

Для scanimage: обойти это можно при помощи опций `-y` (число высоты) `-x` (число ширины) обрезав ширину размера документа:
`-x 165 -y 297`
