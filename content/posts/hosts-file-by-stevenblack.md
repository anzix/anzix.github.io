+++
title = "Применяем Hosts файл от StevenBlack"
date = 2023-03-21T18:22:31+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
Для избавления от рекламы, реферальных ссылок и всяких всплывающих scam сайтов есть обновляемый hosts файл

Сохраняем копию оригинального файла
```sh
cp /etc/hosts ~/Documents/hosts.bak
```
Просто вставляем эту команду
```sh
wget -qO- https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts | sudo tee --append /etc/hosts >/dev/null
```
Пояснение:
> В конце `>/dev/null` значит что команда будет выполняться без вывода подробной информации (полезная команда которая может пригодится для написания конфигов)

