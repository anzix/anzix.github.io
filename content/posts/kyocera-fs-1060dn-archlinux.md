+++
title = "!Принтер KYOCERA FS-1060DN на Arch Linux"
date = 2023-03-06T20:40:11+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

При подключении dmesg выводил

```txt
[ноя 9 16:35] usb 1-1.1: new high-speed USB device number 4 using ehci-pci
[  +0,079051] usb 1-1.1: New USB device found, idVendor=0482, idProduct=0494, bcdDevice= 2.27
[  +0,000003] usb 1-1.1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[  +0,000002] usb 1-1.1: Product: FS-1060DN
[  +0,000001] usb 1-1.1: Manufacturer: Kyocera
[  +0,000001] usb 1-1.1: SerialNumber: NW92Z02857
[  +0,009150] usblp 1-1.1:1.0: usblp0: USB Bidirectional printer dev 4 if 0 alt 0 proto 2 vid 0x0482 pid 0x0494
[  +0,000031] usbcore: registered new interface driver usblp
```

```bash
lsusb
```

```txt
Bus 001 Device 004: ID 0482:0494 Kyocera Corp. FS-1060DN
```

Устанавливаем пакеты первой необходимости

```bash
sudo pacman -S cups cups-pdf
```

Активируем сервис systemd

```bash
sudo systemctl enable --now cups.service
```

После активации вы можете посетить локально созданный веб интерфейс для управления задачами принтера (необходимы реквизиты хост машины) `http://localhost:631/`

Далее нам нужен Aur пакет драйвер для нашего принтера, [kyocera-cups](https://aur.archlinux.org/packages/kyocera-cups) мне не удалось установить

```txt
curl: (22) The requested URL returned error: 404
==> ОШИБКА: Ошибка при загрузке 'https://www.kyoceradocumentsolutions.us/content/download-center-americas/us/drivers/drivers/KyoceraLinuxPackages_20220203_tar_gz.download.gz'
    Прерывание...
 -> ошибка сборки: kyocera-cups
```

Поэтому я решил установить aur пакет [kyocera-print-driver](https://aur.archlinux.org/packages/kyocera-print-driver) который установился успешно

```bash
yay -S kyocera-print-driver
```

После чего устанавливаем мастер настроек для принтера

```bash
sudo pacman -S system-config-printer
```

Для вызова мастера настроек

1. В меню вызова приложений: Параметры печати (Параметры печати)
2. В терминале вводим: `system-config-printer`

![](/images/kyocera-fs-1060dn-archlinux/1667994357.png)
![](/images/kyocera-fs-1060dn-archlinux/1667995429.png)

После успешного добавления принтера пробую распечатать 1 пробную страницу

<span style="color:red">Произошла ошибка при печати</span>

Вывод в dmesg с segmentation fault

```txt
....
[  +0,097262] rastertokpsl[76872]: segfault at 740064009f ip 000000000040c182 sp 00007ffeb8c77e80 error 4 in rastertokpsl[400000+30000]
[  +0,000011] Code: 75 0d eb 79 41 ff cc eb 03 45 89 ec 45 31 f6 47 8d 2c 26 b9 02 00 00 00 48 89 df 44 89 e8 99 f7 f9 41 89 c5 48 98 48 c1 e0 04 <48> 8b 74 05 00 e8 1d 91 00 00 83 f8 00 74 44 45 0f 4c e5 45 0f 4d
....
```

С ошибкой в веб интерфейсе Cups статус **Остановлено "Filter failed"**

Также ВАЖНЫЕ логи cups находятся

```bash
nvim /var/log/cups/error_log
```

```txt
....
PID 1079969 (/usr/lib/cups/filter/rastertokpsl) crashed on signal 11.
....
```

<span style="color:green">РЕШЕНИЕМ для меня</span> было скачать репо [rastertokpsl-re](https://github.com/Fe-Ti/rastertokpsl-re/) и произвести установку
[Сурс решения](https://bbs.archlinux.org/viewtopic.php?id=272961)

```bash
git clone https://github.com/Fe-Ti/rastertokpsl-re
cd rastertokpsl-re
cmake -B_build -H.
cmake --build _build/
./install.sh
```

```txt
Installation completed
```

Ничего перезагружать не нужно просто начните печать снова и всё заработает

Проверял печать на LibreOffice и через браузер PDF файл
