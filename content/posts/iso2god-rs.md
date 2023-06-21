+++
title = "Конвертирование iso в god для взломанной xbox360 утилитой iso2god-rs"
date = 2023-03-03T11:00:14+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Т.к нигде даже в AUR не нашёл подобного софта я покопавшись в github'е наткнулся на форк [iso2god репо написанный на Rust](https://github.com/iliazeus/iso2god-rs) и вместе с [PKGBUILD'ом для Arch Linux](https://github.com/iliazeus/aur/blob/master/iso2god/PKGBUILD) написанный от автора софта

Вводим для скачивания
```bash
curl https://raw.githubusercontent.com/iliazeus/aur/master/iso2god/PKGBUILD
```
После, собираем пакет соглашаясь с установкой пакета
```bash
makepkg -si
```
Однако после запуска через консоль произошла ошибка
```sh
~/Downloads ❯ iso2god
iso2god: error while loading shared libraries: libssl.so.1.1: cannot open shared object file: No such file or directory
```
Для исправления данной ошибки необходим был пакет openssl-1.1
```bash
sudo pacman -S openssl-1.1 lib32-openssl-1.1
```

Для вызова мануала утилиты вводим
```bash
iso2god -h
```
Покажу на примере iso образа Fight Night Champion iXtreme LT+
> Примечание: Если вылезает ошибка
```sh
0: error sending request for url (http://xboxunity.net/... operation timed out
```
Попробуйте запустить с опцией ``--offline``

```bash
iso2god FNС/Fight\ Night\ Champion.iso FNС
```
```sh
extracting ISO metadata
querying XboxUnity for title ID 45410915

    Type: Xbox 360 title
Title ID: 45410915
    Name: FIGHT NIGHT CHAMPION

clearing data directory
writing part files
writing part  0 of 45
writing part  1 of 45
.....
writing part 44 of 45
calculating MHT hash chain
writing con header
done
```

После успешной конвертации в папке где находится образ будет находится папка с title id игры 45410915


Некоторые iso файлы не могут быть сконвертированы по причине отсутствия default.xex
```sh
extracting ISO metadata
Error: default.xex file not found
```

Если это происходит попробуйте найти другую раздачу

