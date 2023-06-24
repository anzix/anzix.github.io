+++
title = "Bluetooth на Linux"
date = 2023-03-10T23:16:34+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Я использую [USB Bluetooth свисток заказанный с Ali](https://megabonus.com/y/MCb8s) с чипом rtl8761buv

Из коробки свисток сразу работает без танцев с бубном (на ранних ядрах 4.9 и 5.10 у меня были проблемы). Главное установить необходимые пакеты, запустить сервис и начать пользоваться.

Качаем самые необходимые пакеты

```sh
sudo pacman -S bluez bluez-utils
```

Запускаем systemd сервис bluetooth

```sh
sudo systemctl enable --now bluetooth
```

И проверяем работоспособность сервиса

```sh
systemctl status bluetooth
```

Запускаем введя в терминал

```sh
bluetoothctl
```

Сканируем

```
[bluetooth]# scan on
```

Чтобы устройство автоматически соединялся при включении необходимо чтобы устройство было доверенным, и это всё что необходимо.

```
[bluetooth]# trust [Mac адрес устройства]
```

Если нашли устройство к которому можно соединится

```
[bluetooth]# connect [MAC адрес устройства]
```

Если хотите менеджер Bluetooth с интерфейсом

```sh
sudo pacman -S blueman
```

## Автозапуск Bluetooth при старте

Включаем автозапуск в конфиге, просто вставляем эту команду и всё

```sh
sudo sed -i 's|#AutoEnable=false|AutoEnable=true|g' /etc/bluetooth/main.conf
```
