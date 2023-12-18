+++
title = "!Как играть нативно в M.U.G.E.N'ы на Linux"
date = 2023-07-11T14:30:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

- [Ikemen-GO Github страница](https://github.com/ikemen-engine/Ikemen-GO)

Ikemen-GO - Это fighting движок с открытым исходным кодом, поддерживающий ресурсы M.U.G.E.N.\
Имеет множество улучшений таких как широкоформатное разрешение с поддержкой скейлинга спрайтов

Я буду показывать на примере установки с последней git ветки, скомпилировав бинарный файл\

> Для тех кому не хочется компилировать бинарный файл, это также можно сделать и с уже собранным архивом вместе с скринпаком находящийся в Release, хоть работа на этом методе не проверена.

Открываем терминал и клонируем репо, переходим в него и компилируем

```sh
git clone https://github.com/ikemen-engine/Ikemen-GO.git && cd Ikemen-GO
make -j 11 Ikemen_GO_Linux
```

После завершения копирую всё ниже перечисленное в ваш Mugen папку (их ещё называют screenpack'и)\
На моём примере это будет записанный на болванку в 2012 году **Naruto Storm M.U.G.E.N 2010**

```txt
bin/Ikemen_GO_Linux
font
external
data
```

Переходим в **Naruto Storm M.U.G.E.N 2010** и запускаем командой

```sh
./Ikemen_GO_Linux
```

Всё работает но только шрифтов в настройках и в паузе нет, в логах показано что отсутствуют файлы

```txt
2023/07/10 21:52:28 failed to load f-6x9.def (screenpack font): File not found
2023/07/10 21:52:29 failed to load Open_Sans.def (screenpack font): File not found
```

Копирую из [скринпака](https://github.com/ikemen-engine/Ikemen_GO-Elecbyte-Screenpack) отсутствующие файлы

```txt
font/f-6x9.def
font/Open_Sans.def
font/f-6x9.sff # Тоже необходим
```

И всё работает прям как нативно, и это без использования wine

![image](/images/native-mugen-on-linux/Screenshot_20230711_014741.png)
![image](/images/native-mugen-on-linux/Screenshot_20230711_015713.png)
![image](/native-mugen-on-linux/Screenshot_20230711_015511.png)
![image](/images/native-mugen-on-linux/Screenshot_20230711_014802.png)
