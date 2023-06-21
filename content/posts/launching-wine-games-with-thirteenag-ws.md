+++
title = "!Запуск игр через wine с модом на Широкоформат от ThirteenAG"
date = 2023-03-14T23:35:05+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

* https://github.com/ThirteenAG/WidescreenFixesPack

## Со Steam'а

[Качаем патч для MP1](https://github.com/ThirteenAG/WidescreenFixesPack/releases/tag/mp1) распаковываем и всё закидываем в папку MP1

Открываем параметры Steam MP1 и прописываем:
```sh
WINEDLLOVERRIDES="d3d8=n,b" %command%
```
После чего игра будет в широкоформате и с рабочим Mangohud'ом

## !Без Steam'а

* [Решение частично было взято от сюда](https://github.com/ThirteenAG/WidescreenFixesPack/issues/590)

Модифицируемые сборки [Max Payne 1](https://steamcommunity.com/sharedfiles/filedetails/?id=2392432694) и [Max Payne 2](https://steamcommunity.com/sharedfiles/filedetails/?id=2391895389) от Philips_27 не могли запустится в широком разрешении, лишь только в растянутом 16x9 разрешении

<span style="color:green">Решение</span>: Прописываем в терминале `winecfg`

Во вкладке "Библиотеки"  добавляем DLL'ки из списка
* Для MP1 - **d3d8 (сторонняя, встроенная)**
* Для MP2 - **d3d9 (сторонняя, встроенная)**

И всё, добавляем в Lutris и играем

