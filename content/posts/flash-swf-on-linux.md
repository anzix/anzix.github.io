+++
title = "Запуск локальных .swf файлов Flash на Arch Linux"
date = 2023-03-03T12:16:29+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

![image](/images/flash-swf-on-linux/1667684021.png)

Решил поностальгировать и протестировать работоспособность flash игр.
AUR пакет [flashplayer-standalone](https://aur.archlinux.org/packages/flashplayer-standalone) справляется со всеми задачами

```bash
yay -S flashplayer-standalone
```

Однако есть <span style="color:red">проблемы</span> с отставанием звука в любых играх но есть <span style="color:green">РЕШЕНИЕ</span>, качаем [AUR пакет ruffle](https://aur.archlinux.org/packages/ruffle-nightly-bin)

```bash
yay -S ruffle-nightly-bin
```

Использование:

```bash
ruffle [swf файл]
```
