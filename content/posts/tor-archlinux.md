+++
title = "Tor Archlinux"
date = 2023-03-08T00:42:54+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Источники

- [Ctlos Wiki](https://ctlos.github.io/wiki/packages/other-pkg/#tor)
- [Arch Wiki](https://wiki.archlinux.org/title/User:Katoumegumi#Using_Tor_bridges)

Установка

```sh
sudo pacman -S tor torsocks
yay -S obfs4proxy
```

```sh
sudo systemctl start tor
sudo systemctl status tor
```

Видим что застряло на 5%, т.к провайдер блокирует тор

![image](/images/tor-archlinux/tor-status.png)

```sh
sudo mkdir -p /etc/torrc.d/
sudo -e /etc/tor/torrc
```

Раскоментируем `%include /etc/torrc.d/*.conf` и сохраняем

В данном файле указываем строки

```sh
sudo -e /etc/torrc.d/custom.conf
```

```txt
UseBridges 1
ClientTransportPlugin obfs4 exec /usr/bin/obfs4proxy
Bridge [!данные из bridges@torproject.org]
```

> ! Заводим доп. акк гугл и пишем в gmail на этот ящик bridges@torproject.org оставив пустой тему а в сообщении вводим "get transport obfs4" (данные из которых у меня заработал tor)"

Перезапускаем tor

```sh
sudo systemctl restart tor
```

Если в статусе видите данную строку значить пробуйте другие даные из bridges@torproject.org

```txt
Proxy Client: unable to connect OR connection (handshaking (proxy))
```

Если до сих пор не работает tor, тогда производим данные манипуляции описанные в

- https://wiki.archlinux.org/title/Tor#Problem_with_user_value

```sh
sudo nano /usr/lib/systemd/system/tor.service
```

Добавляем в `[Service]` и сохраняем

```txt
User=root
Group=root
Type=simple
```

Выполняем эти команды

```sh
sudo chown -R tor:tor /var/lib/tor/
sudo chmod -R 700 /var/lib/tor
```

После чего перезапускаем tor

```
sudo systemctl daemon-reload
sudo systemctl restart tor
```

Вы должны увидеть в статусе загрузку (bootstrapped) до 100%. Это значит что узел тор запущен
![image](/images/tor-archlinux/tor-works.png)

## Работа Tor вместе с Обход блокировок Рунета проксировать домены .onion

Если данное расширение у вас уже установлено, необходимо только активировать чекбокс "**Использовать СВОЙ локальный Tor**" также (<span style="color:green">РЕКОМЕНДУЕТСЯ для безопасности</span>) во вкладке "Модификаторы" поставить чекбокс "**Проксировать только HTTPS-сайты**"

![image](/images/tor-archlinux/anticensority.png)

## Proxy SwitchyOmega + Tor

- [Proxy SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)

Запускаем тор

```sh
sudo systemctl start tor
```

Открываем настройки расширения и редактируем профиль proxy. Переименовываем его в "Tor"

- Protocol - SOCKS5
- Server - 127.0.0.1
- Port - 9050

![image](/images/tor-archlinux/switchyomega-tor.png)

После чего применяем изменения и пробуем протестировать на https://check.torproject.org/

В быстром меню расширения жмём на уже настроенный прокси и теперь браузер перенаправляет на трафик тор

![image](/images/tor-archlinux/switchyomega-menu.png)

Можно использовать вместе с Torrent Search

### Открывать сайты только \*.onion сайты данными проксями

Для этого открываем настройки расширения, создаём новый профиль "New profile"

- Имя профиля - Onion
- Тип профиля - Switch Profile

Жмём Create

![image](/images/tor-archlinux/creating-new-profile.png)

И указываем как показано

- Condition Type - Host wildcard
- Condition Details - .onion
- Profile - наш Tor прокси из списка

Жмём Apply Changes

![image](/images/tor-archlinux/switchyomega-rules-tor.png)

В иконке расширения выбираем наш созданный Switch Profile - onion и всё

![image](/images/tor-archlinux/switchyomega-menu-onion.png)

Теперь прокси сами будут включатся когда будут обрабатывать запросы на .onion сайты

## FoxyProxy Standard + Tor

- https://addons.mozilla.org/ru/firefox/addon/foxyproxy-standard

Для Firefox

При установке расширения открываем настройки и жмём "Добавить"

- Название - Tor
- Тип прокси - SOCKS5
- Прокси IP адрес - 127.0.0.1
- Порт - 9050

Жмём Сохранить и редактировать шаблоны

- Название - onion
- Шаблон - \*.onion
- Тип - Wildcard
- HTTP/s - all (Для большей безопасности <span style="color:green">РЕКОМЕНДУЕТСЯ</span> проксировать только HTTPS запросы, HTTP сайты <span style="color:red">могут читаться и изменяться конечными участниками сети Tor!</span>)

![image](/images/tor-archlinux/foxyproxy-https.png)

Жмём на иконку расширения и выбираем "Использовать прокси по шаблону и прядку"

![image](/images/tor-archlinux/foxyproxy-setup.png)

Теперь это расширение будет в обычном режиме обрабатывать обычные запросы (без прокси), а когда вы будете открывать сайты .onion расширение автоматически будет включать данные прокси Tor
