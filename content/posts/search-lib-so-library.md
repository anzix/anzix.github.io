+++
title = "Как найти пакет с недостающей lib...so.. библиотекой?"
date = 2023-03-03T12:12:56+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Качаем пакет

```bash
sudo pacman -S pkgfile
```

Обновляем датабазу

```bash
sudo pkgfile -u
```

Ищем пакет по библиотеке

```bash
pkgfile -sr libssl.so.3
```

```sh
core/openssl
multilib/lib32-openssl
```

Устанавливаем недостающие библиотеки с помощью pacman

```bash
sudo pacman -S openssl lib32-openssl
```
