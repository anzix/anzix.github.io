+++
title = "ZeroTier - как поиграть в Minecraft с другом используя Arch Linux"
date = 2023-03-01T13:55:09+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Ориентировался на данные источники

- https://www.youtube.com/watch?v=RGTzRTC1JxM&t=634s
- https://www.youtube.com/watch?v=JXB6FHJsqOw
- https://wiki.archlinux.org/title/ZeroTier

Плюсы:

1. Не требует проброса портов

Качаем пакет

```bash
sudo pacman -S zerotier-one
```

Запускаем systemd сервис zerotier

```bash
sudo systemctl enable --now zerotier-one.service
```

Чтобы узнать id своего компьютера

```bash
sudo zerotier-cli info
```

```sh
# Пример выхлопа
200 info 89e92ceee5 1.2.4 ONLINE
```

[Регаемся в ZeroTier](https://www.zerotier.com/)

В браузере залогинившись и подтвердив почту жмём на странице ZeroTier <span style="color:#9a5500">Create A Network</span> и кликаем на созданную сеть

На ПК коннектимся используя команду
(_network_id_ указан в странице ZeroTier в **Basics**)

```bash
sudo zerotier-cli join network_id
```

Что я сделал на главной странице ZeroTier Central:

1. Поменял авто назначение **IPv4 Auto-Assign**
2. **(Обязательно)** Отмечаем галочкой в столбце **Auth?** наш подключённый ПК Arch Linux дабы аутентифицировать его в сети

Создаю каталог для размещения Minecraft сервера

```bash
mkdir ~/Server_[version]
```

Скачиваем [server.jar](https://www.minecraft.net/ru-ru/download/server) и помещаем его в созданную папку

```bash
cd ~/Server_[version]
```

Пробный запуск сервера в консоли (У меня 16Gb ОЗУ поэтому я выделил столько памяти серверу)

```bash
java -Xmx6124M -Xms6124M -jar server.jar nogui
```

Создастся необходимые файлы и потом попросит принять EULA
Редактируем внутри папки сервера `eula.txt`

```
eula=true
```

Редактируем `server.properties`

```bash
...
online-mode=false # Для пираток
max-players=3
motd=Test Arch Linux Anix # Это описание сервера
...
```

Останавливаем сервер
<kbd><kbd>CTRL</kbd>+<kbd>c

И запускаем снова

```bash
java -Xmx6124M -Xms6124M -jar server.jar nogui
```

Запускаем [tlauncher](https://aur.archlinux.org/packages/tlauncher): ForgeOptifine [version]
После запуска майна вводим в "Прямое подключение" для проверки сервера

```
localhost
```

## Подключение друга на Windows:

Он должен скачать Windows версию клиента ZeroTier, после скачивания в трее будет <span style="color:yellow">жёлтый значок</span>, жмём на него и выбираем **Join Network...**

Даём другу ваш _network_id_ который указан на странице ZeroTier в **Basics**

**(Обязательно)** На странице браузера ZeroTier отмечаем галочкой в столбце **Auth?** дабы аутентифицировать ПК друга

В консоли нашего ПК вводим

```bash
ifconfig
```

Видим новое созданное уст-во **ztrta2ogfu**.
Копируем **inet** ip и даём вашему другу с прописанным портом майна 25565.
Он вставляем этот ip в майн, и Готово!
