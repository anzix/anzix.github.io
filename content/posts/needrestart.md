+++
title = "Needrestart - Paman Хук на необходимость перезагрузки после обновления"
date = 2023-03-02T18:44:55+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Устанавливаем пакеты
```bash
yay -S needrestart iucode-tool
```
```bash
sudo -e /etc/pacman.d/hooks/99-needrestart-pacman.hook
```
Вставляем
```sh
[Trigger]
Operation = Install
Operation = Upgrade
Operation = Remove
Type = Package
Target = *
[Action]
Description = Checking which daemons need to be restarted after library upgrades.
When = PostTransaction
Exec = /usr/bin/needrestart
Depends = needrestart
```

Вот как это выглядит
![](/images/needrestart/1669990137.png)

