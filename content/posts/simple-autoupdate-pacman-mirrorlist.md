+++
title = "Простое автообновление зеркал Pacman mirrorlist"
date = 2023-03-22T14:08:01+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Хук mirrorupgrade будет выполнятся после того как обновится пакет pacman-mirrorlist при обновлении системы

После этого пробуждает reflector.service который в свою очередь выполняет обновления зеркал основываясь на настройках конфига `/etc/xdg/reflector/reflector.conf`, этот конфиг тоже необходимо отредактировать.

Просто копируем данные команды и вставляем в терминал
```sh
sudo sed -i "s/^--protocol.*/--protocol http,https/" /etc/xdg/reflector/reflector.conf
sudo sed -i "s/# --country.*/--country ru,by/" /etc/xdg/reflector/reflector.conf
sudo sed -i "s/^--latest.*/--latest 12/" /etc/xdg/reflector/reflector.conf
sudo sed -i "s/^--sort.*/--sort rate/" /etc/xdg/reflector/reflector.conf
```
```sh
sudo bash -c 'cat <<EOF > /etc/pacman.d/hooks/mirrorupgrade.hook
[Trigger]
Operation = Upgrade
Type = Package
Target = pacman-mirrorlist

[Action]
Description = Updating pacman-mirrorlist with reflector and removing pacnew...
When = PostTransaction
Depends = reflector
Exec = /bin/sh -c 'systemctl start reflector.service; if [ -f /etc/pacman.d/mirrorlist.pacnew ]; then rm /etc/pacman.d/mirrorlist.pacnew; fi'
EOF'
```
