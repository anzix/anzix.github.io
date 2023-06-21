+++
title = "!Интернет подключение по проводу без роутера DSL (PPPoE)"
date = 2023-03-09T22:22:24+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Мне помог [этот гайд из группы ВК по Арчу](https://vk.com/topic-129498031_39721608) (Хотя он изначально почему-то не работал, странно)

А вообще этот метод описан в [Arch Wiki по netctl](https://wiki.archlinux.org/title/netctl#Wired_2)

Скачиваем пакет [netctl](https://archlinux.org/packages/core/any/netctl/) с зависимостями [openresolv](https://archlinux.org/packages/core/any/openresolv/)

> Если вы в офлайне тогда качаем эти 2 пакета (нажав **Download From Mirror**) с устройства у которого есть инет и передаём по usb и устанавливаем используя `sudo pacman -U`

Копируем конфиг

```sh
sudo cp /etc/netctl/examples/pppoe/ etc/netctl/
```

И редактируем его

```sh
nano /etc/netctl/pppoe
```

```txt
Interface=вашинтерфейс
User=’логин провайдера’
Password=’пароль провайдера’
```

В “_Interface_” меняем на наш из команды `ip link` в терминале

Логин и пароль должны быть в кавычках ''

Сохраняем и выходим

Отключаем сервис dhcpcd чтобы не возникало конфликта

```sh
sudo systemctl disable dhcpcd
```

Запускаем

```sh
sudo netctl start pppoe
```

> Я эти действия не проводил, у меня сразу всё встало.
>
> Если ошибка, тогда вводим в терминале
> `ip link set (ваш сетевой интерфейс) down`
>
> Повторяем запуск netctl

Проверяем наличие интернета

У меня немного по началу долго грузил вкладки минуты 2, но потом перестало и начал грузить мгновенно

При перезагрузке ПК необходимо всегда вручную писать команду

```sh
sudo netctl start pppoe
```

Пока не знаю как сделать подключение автоматическим

## Возвращаем обратно подключение по роутеру (Dhcpcd)

Но если вы хотите подключится обратно по роутеру, тогда отключаем pppoe
командой

```sh
sudo netctl disable pppoe
```

И подключив провод в роутер а затем в пк, запускаем обратно службу dhcpcd

```sh
sudo systemctl enable --now dhcpcd
```

У меня инет сразу заработал, без ребута
