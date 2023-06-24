+++
title = "!Миграция правил iptables в nftables"
date = 2023-03-04T20:44:20+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

[Офф гайд](https://wiki.nftables.org/wiki-nftables/index.php/Moving_from_iptables_to_nftables)

К примеру чтобы правила iptables на [обход блокировок трекеров bt](/posts/torrent-setup-and-migration/#%D0%BE%D0%B1%D1%85%D0%BE%D0%B4-%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BA%D0%B8-bt-%D1%82%D1%80%D0%B5%D0%BA%D0%B5%D1%80%D0%BE%D0%B2). перешли к nftables

Убедитесь что у вас установлен nftables

```bash
sudo pacman -S nftables
```

Вводим данные команды для перевода правил пригодный для nftables

```bash
sudo iptables-save > iptables_rules.txt
iptables-restore-translate -f iptables_rules.txt > nft_ruleset.nft
```

Отключаем iptables

```bash
sudo systemctl disable --now iptables.service
```

Создаём backup файл nftables.conf

```bash
sudo cp /etc/nftables.conf /etc/nftables.conf.bak
```

Немного правим конфиг

```bash
sudo bash -c 'cat << "EOF" > /etc/nftables.conf
#!/usr/bin/nft -f
# vim:set ts=2 sw=2 et:

flush ruleset
EOF'
```

Импортируем правила (iptables) в конфиг nftables.conf

```bash
sudo sh -c "cat $HOME/nft_ruleset.nft >> /etc/nftables.conf"
```

Применяем

```bash
sudo nft -f /etc/nftables.conf
```

Просмотреть список правил

```bash
sudo nft list ruleset
```

Автозапуск правил после запуска ПК

```bash
sudo systemctl enable nftables
```
