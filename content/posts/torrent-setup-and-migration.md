+++
title = "Торрент клиенты и их настройка"
date = 2023-03-10T23:37:30+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

## Qbittorrent файлы из Windows 10 на Linux

[Папка в которой находятся торрент файлы](https://github.com/qbittorrent/qBittorrent/wiki/Frequently-Asked-Questions#Where_does_qBittorrent_save_its_settings)

Закидываем все файлы из Windows директории qbittorrent `C:\Users\<username>\AppData\Local\qBittorrent\BT_backup`

В
`~/.local/share/qBittorrent/BT_backup`

Потом вручную в клиенте править пути к уже закачанному контенту т.к я не так много раздаю весь этот процесс не такой затруднительный.

## Миграция с QBitTorrent на Transmission

Отдельно закидываем файлы с расширением .torrent
`~/.config/transmission/torrents`

Отдельно закидываем файлы с расширением .fastresume
`~/.config/transmission/resume`

После чего при запуске GUI торренты будут видны и все разом начнут скачиваться в `~/Downloads`, поэтому СРАЗУ приостанавливаем все через контекстное меню тулбара

Файл - Приостановить все

Теперь необходимо указать каждый торрент отдельно папку уже закачанного. Жмём RMB на торрент из списка и выбираем - Указать расположение...

Обязательно ставим галочку "Не перемещать" и жмём "Применить"

![](/images/torrent-setup-and-migration/transmission-1.png)

![](/images/torrent-setup-and-migration/transmission-2.png)

Далее RMB на торрент и жмём "Проверить локальные данные" и ждём
После чего можно начать раздавать данный торрент нажав "Запустить"

![](/images/torrent-setup-and-migration/transmisison-3.png)

И так с каждым торрентом из списка

## Смена passkey для qbittorrent утилитой PasskeyChanger от panter-dsd

- https://github.com/panter-dsd

![](/images/torrent-setup-and-migration/passkey-changer.png)

Данное программное обеспечение написано на qt4, поэтому нам нужно скачать скомпилированный бинарный [пакет qt4 из AUR](https://aur.archlinux.org/packages/qt4-bin)

```sh
yay -S qt4-bin
```

[Оригинальный PKGBUILD](https://github.com/aur-archive/passkeychanger/blob/master/PKGBUILD) не удаётся построить из-за других сумм md5 и sha256. Я [правил PKGBUILD](https://gitlab.com/anz1x/passkeychanger/-/blob/main/PKGBUILD) и теперь пакет собирается нормально

Установка PasskeyChanger

```sh
git clone https://gitlab.com/anz1x/passkeychanger.git
cd passkeychanger
makepkg -si
```

После установки в терминале вводим для вызова GUI

```sh
passkeychanger
```

Прога работает я проверял

## Обход блокировки bt\*. трекеров

- https://rutracker.org/forum/viewtopic.php?t=5134313

Немного инфы:

> Данные bt\*. трекеры (служебные серверы для файлообмена) всеми известного торрент трекера RuTracker Org

**Требования**:

> При использовании данного способа - все остальные способы обхода блокировки трекеров нужно обязательно выключить! (если использовали прокси в настройках клиента - отключите его, поставьте Тип прокси - Нет)

На примере QBitTorrent

![](/images/torrent-setup-and-migration/qbittorrent.png)

### С помощью файла hosts

Способ универсальный, подходит под любую систему Linux, Windows, Mac.
Hosts файл находится по этому пути, редактируем его

```sh
sudo vim /etc/hosts
```

И вставляем

```txt
185.15.211.203 bt.t-ru.org
185.15.211.203 bt2.t-ru.org
185.15.211.203 bt3.t-ru.org
185.15.211.203 bt4.t-ru.org
```

После этого перезагружаем пк и смотрим на результат

До

![](/images/torrent-setup-and-migration/before.png)

После

![](/images/torrent-setup-and-migration/after.png)

### Через iptables

Вставляем данную команду

```sh
sudo iptables -t nat -A OUTPUT -p tcp -m tcp --dport 80 -d 195.82.146.120/30 -j DNAT --to-destination 185.15.211.203:80
```

Просмотреть применённое правило

```sh
sudo iptables -vL -t nat
```

```txt
....
Chain OUTPUT (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 DNAT       tcp  --  any    any     anywhere             195.82.146.120/30    tcp dpt:http to:185.15.211.203:80
....
```

Чтобы данное правило (rules) работало при перезагрузке системы необходимо применить данные команды

```sh
sudo iptables-save -f /etc/iptables/iptables.rules
sudo systemctl enable iptables.service
```

Пояснение:

> Первая команда запишет все применённые правила в файл который читает iptables.service, который в свою очередь мы добавили в автозапуск

Ребутимся

### !Через NFTabels

Скачиваем пакет nftables

```sh
sudo pacman -S nftables
```

Добавляем правило по обходу bt\*. трекеров

```sh
sudo nft add rule ip nat OUTPUT ip daddr 195.82.146.120/30 tcp dport 80 counter dnat to 185.15.211.203:80
```

Для проверки посмотрим на список правил

```sh
sudo nft list ruleset
```

Создаём backup файл nftables.conf

```sh
sudo cp /etc/nftables.conf /etc/nftables.conf.bak
```

Немного правим конфиг

```sh
sudo bash -c 'cat << "EOF" > /nft_ruleset.nft
#!/usr/bin/nft -f
# vim:set ts=2 sw=2 et:

flush ruleset

EOF'
```

Записываем правила в конфиг файл

```sh
sudo nft -s list ruleset >> nft_ruleset.nft
sudo sh -c "cat $HOME/nft_ruleset.nft >> /etc/nftables.conf"
```

Автозапуск правил после запуска ПК

```sh
sudo systemctl enable nftables
```

#### Как удалить правило в nftables

К примеру у вас в выводе `sudo nft list ruleset` два одинаковых правила

- [Пример из Arch Wiki](https://wiki.archlinux.org/title/Nftables#Deletion)

```txt
# nft --handle --numeric list chain inet my_table my_input
table inet my_table {
     chain input {
          type filter hook input priority 0;
          ip saddr 127.0.0.1 accept # handle 10
     }
}
```

Т.е если сравнить из данного примера (полагаясь из вывода команды `sudo nft list ruleset`) то выглядит это так

```sh
sudo nft -an list chain ip nat OUTPUT
```

```txt
table ip nat {
	chain OUTPUT { # handle 3
		type nat hook output priority -100; policy accept;
		ip daddr 195.82.146.120/30 tcp dport 80 counter packets 0 bytes 0 dnat to 185.15.211.203:80 # handle 6
		ip daddr 195.82.146.120/30 tcp dport 80 counter packets 0 bytes 0 dnat to 185.15.211.203:80 # handle 18
```

В выводе данной команды заметили так называемый `handle_value` (рукоятка и её значение, у меня это **18**) это значение из дубликата правила вводим в конце команды для удаления показанной ниже

```sh
sudo nft delete rule ip nat OUTPUT handle 18
```
