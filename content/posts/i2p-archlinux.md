+++
title = "!i2p на Arch Linux"
date = 2023-03-07T13:21:50+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
* https://wiki.archlinux.org/title/I2P_(%D0%A0%D1%83%D1%81%D1%81%D0%BA%D0%B8%D0%B9)

Качаем пакет
```sh
sudo pacman -S i2pd
```
Запускаем сервис
```sh
sudo systemctl start i2pd
```

Переходим на `http://127.0.0.1:7070/`

![](/images/i2p-archlinux/Screenshot_20230307_212912.png)

* [Из статьи](https://habr.com/ru/post/550072/)

> *Firewalled: Является индикатором недоступности порта UDP извне. Может быть сигналом о блокировке рабочего порта I2Pd файерволом операционной системы. В большинстве случаев статус «Firewalled» видят пользователи за NAT-сервером, не имеющие выделенного IP-адреса: главным образом пользователи сотовых операторов.*

Но даже в таком режиме всё работает

Для активации проксирования i2p сайтов в браузере chromium будем юзать [Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)

Заходим в настройки расширения и создаём новый **Proxy Profile** (**Tunneling traffic through proxy servers**) назвав его в **Profile name** "i2p"

Правим в данном профайле как показано ниже и сохраняем
* Protocol - HTTP
* Server - 127.0.0.1
* Port - 4444

Затем переименовываем (из [гайда по SwitchyOmega + Tor](/posts/tor-archlinux/#proxy-switchyomega--tor)) созданный SwitchProfile под названием onion и редактируем его в "onion + i2p"

![](/images/i2p-archlinux/switchy-omega.png)

Всё осталось только активировать его и зайти на какой-нибудь i2p сайт, я для примера смог зайти на [ArchLinux i2p](http://archlinux.i2p/)

![](/images/i2p-archlinux/switchy-omega-1.png)
![](/images/i2p-archlinux/Screenshot_20220628_192053.png)
