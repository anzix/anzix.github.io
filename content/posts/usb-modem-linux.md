+++
title = "!!Usb Modem Arch Linux"
date = 2023-03-02T12:58:27+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Загружаешься с live usb, подключешь модем, чрутаешься в арч, ставишь, что\
тебе нужно. Профит! Свой модем я подрубал просто отправив `AT` команду
(видимо используя minicom) в порт и подняв `dhcp`.

Консоль для доступа к USB модему

```bash
sudo pacman -S minicom
```
