+++
title = "!!Usb Modem Arch Linux"
date = 2023-03-02T12:58:27+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

## Подключение и использование USB модема

Загружаешься с live usb, подключешь модем, чрутаешься в арч, ставишь, что\
тебе нужно. Профит! Свой модем я подрубал просто отправив `AT` команду
(видимо используя minicom) в порт и подняв `dhcp`.

Консоль для доступа к USB модему

```bash
sudo pacman -S minicom
```

## Прошивка/Разблокировка USB модема

- [Arch Wiki](https://wiki.archlinux.org/title/Mobile_broadband_modem)
- [Разблокировка модема](https://wiki.archlinux.org/title/Mobile_broadband_modem#AT_commands)

`AT^CARDLOCK="NCK-код"` - разблокировка модема. NCK-код должен быть вычеслен\
по IMEI. После этого модем может работать с любым GSM-провайдером (т.е операторами\
модемов (Мегафон, Билайн и т. п.) с сим-картами других операторов).

Для вычисления NCK-кода по имей необходим калькулятор кодов

[Онлайн калькулятор для USB модемов Huawei](http://calc.gmss.ru/index.php)

### Графические нативные программы на Linux

[Huawei_modem_calculator_v2](https://github.com/bigbigmdm/Huawei_modem_calculator_v2)

Установка и компиляция:

```sh
# Качаем зависимости для работы программы
sudo pacman -S gcc-libs glibc openssl qt5-base qt5-serialport
# Клонируем
git clone https://github.com/bigbigmdm/Huawei_modem_calculator_v2 && cd Huawei_modem_calculator_v2
# Меняю на использование динамической библиотеки openssl в CMakeFiles.txt
# это исправляет ошибку компиляции
sed -i CMakeLists.txt -e 's/\(set(OPENSSL_USE_STATIC_LIBS\) TRUE/\1 FALSE/'
# Собираем и компилируем
cmake -S . -B build && cd build
make -j`nproc`
```

Появиться исполняемый файл **Huawei_modem_calculator** его можно запустить и работать с ним

## Другая информация и размышления

1. Прошивка модема используя qdl?\
   [Быстрая смена прошивки / Смена IMEI - с помощью QFIL](https://4pda.to/forum/index.php?showtopic=1011903&view=findpost&p=107379188)

2. Возможно использовать для прошивки ZTE usb модемов?\
   [qtools](https://github.com/forth32/qtools)

   В Issues есть упоминание о **ZTE terminal software update framework**
