+++
title = "ZeroTier - как поиграть в Minecraft с другом"
date = 2023-03-01
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Обновлено: 15.12.24

ZeroTier - это своего рода Hamachi где создаётся комната с выделенным ip адресом
которой можно поделится с друзьями и аутентифицировать их для доступа к вашей
комнате

Плюсы:

1. Не требует проброса портов

## Установка, настройка и запуск

Качаем пакет

```bash
sudo pacman -S zerotier-one
```

Запускаем systemd сервис zerotier

```bash
sudo systemctl enable --now zerotier-one.service
```

> TODO: необходимо проверить
>
> Чтобы иметь возможность пропустить, используя `sudo`
>
> ```sh
> sudo cat /var/lib/zerotier-one/authtoken.secret > ~/.zeroTierOneAuthToken
> ```

Чтобы узнать id своего компьютера

```bash
sudo zerotier-cli info
```

```sh
# Пример выхлопа
200 info 89e92ceee5 1.2.4 ONLINE
```

Теперь [регистрируемся в ZeroTier](https://www.zerotier.com/)

В браузере залогинившись и подтвердив почту на странице ZeroTier жмём **Create
A Network** и кликаем на созданную сеть

На ПК коннектимся используя команду (`network_id` указан в странице ZeroTier в
**Basics**)

```sh
sudo zerotier-cli join network_id
```

Что я сделал на главной странице **ZeroTier Central**:

1. Поменял авто назначение **IPv4 Auto-Assign**
2. **(Обязательно)** Отмечаем галочкой в столбце **Auth?** наш подключённый ПК
   Arch Linux дабы аутентифицировать его в сети

Создаю каталог для размещения Minecraft сервера

```sh
mkdir ~/Server_[version]
```

Скачиваем [server.jar](https://www.minecraft.net/ru-ru/download/server) и помещаем
его в созданную папку

```sh
cd ~/Server_[version]
```

Пробный запуск сервера в консоли (У меня 16Gb ОЗУ поэтому я выделил столько памяти
серверу). Это создаст необходимые файлы и потом попросит принять EULA.

```sh
java -Xmx6124M -Xms6124M -jar server.jar nogui
```

Внутри папки сервера открываем файл `eula.txt` и принимаем EULA указав назначение
`true`

```txt
eula=true
```

Редактируем `server.properties` на содержимое

```ini
...
online-mode=false # Для пираток
max-players=3
motd=Test Arch Linux Anix # Это описание сервера
...
```

Останавливаем сервер комбинацией клавишей CTRL+c, или написав `stop` в терминале
в котором запущен сервер

И запускаем снова командой

```sh
java -Xmx6124M -Xms6124M -jar server.jar nogui
```

Запускаем [Legacy Launcher](https://llaun.ch/): ForgeOptifine [version]. После
запуска майна вводим в "Прямое подключение" `localhost` для проверки сервера

## Подключение друга на Windows

Друг должен скачать Windows версию клиента ZeroTier, после скачивания в трее будет
жёлтый значок, жмём на него и выбираем **Join Network...**

Даём другу ваш `network_id` который указан на странице ZeroTier в **Basics**

**И обязательно** на странице браузера ZeroTier отмечаем галочкой в столбце
**Auth?** дабы аутентифицировать ПК друга

В консоли нашего хост ПК вводим `ifconfig`

Видим новое созданное уст-во **ztrta2ogfu**. Копируем **inet** ip и даём вашему
другу с прописанным портом майна 25565. Друг вставляем этот `ip` в майн, и
готово!

## Ссылки

- [Видео инструкция 1](https://www.youtube.com/watch?v=RGTzRTC1JxM&t=634s)
- [Видео инструкция 2](https://www.youtube.com/watch?v=JXB6FHJsqOw)
- [Arch Wiki ZeroTier](https://wiki.archlinux.org/title/ZeroTier)
