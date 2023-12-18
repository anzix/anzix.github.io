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

## Установка универсального hosts файла

Просто вставляем эту команду

```sh
wget -qO- https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts | sudo tee --append /etc/hosts >/dev/null
```

Пояснение:

> В конце `>/dev/null` значит что команда будет выполняться без вывода подробной информации (полезная команда которая может пригодится для написания конфигов)

## Обновление универсального hosts файла

Я для себя написал небольшую shell функцию которая производит сравнение с источника репозитория StevenBlack и моим текущим hosts файлов и удаляет/добавляет необходимое в него.

Вы можете вставить данную функцию в `.zshrc` и пользоваться ею

```sh
# Обновление уникального hosts файла StevenBlack с обработкой
# 1. Убираю строки localhost (уже есть)
# 2. Убираю все комментарии
# 3. Добавляю отступ следующей строки в начале
uphosts () {
 wget -t 2 -O- https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts \
  | grep '^0\.0\.0\.0' \
  | grep -v '^0\.0\.0\.0 [0-9]*\.[0-9]*\.[0-9]*\.[0-9]*$' \
  | sed '1s/^/\n/' > /tmp/adlist-all

 # Сравниваем содержимое файлов /etc/hosts и /tmp/adlist-all, игнорируя первые три строки
 diff <(tail -n +4 /etc/hosts) /tmp/adlist-all > diff.txt

 # Заменяем содержимое файла /etc/hosts на объединение изменений из diff.txt
 sudo patch /etc/hosts < diff.txt

 # Корректировка правил универсального hosts файла
 sudo sed -i "/^0.0.0.0 clck.ru/s/^/#/g" /etc/hosts # Реф ссылки pepper.ru

 # Удаляем временный файл diff.txt
 rm diff.txt
}
```
