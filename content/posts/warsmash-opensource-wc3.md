+++
title = "!Warsmash (OpenSource WarCraft 3) на Linux"
date = 2023-09-01T14:10:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Частично ориентировался по [этому видео гайду](https://www.youtube.com/watch?v=YWXzSxpgzpY)

Перед началом необходимо установить из AUR пакет

```sh
yay -S jdk17-temurin
```

(Не проверял) Также можно скачать с [офф сайта](https://adoptium.net/download/)

Выдать список доступных библиотек

```sh
archlinux-java status
```

Выставляем глобально версию Java которую рекомендует автор проекта

```sh
sudo archlinux-java set java-17-temurin
```

Если использовать java-17-openjdk и собрать проект, при запуске собранного файла  будет ошибка

```sh
$ ./bin/warsmash
Picked up _JAVA_OPTIONS: -Djava.util.prefs.userRoot=/home/anix/.config/java
Warsmash engine is starting...
Inconsistency detected by ld.so: dl-lookup.c: 107: check_match: Assertion `version->filename == NULL || ! _dl_name_match_p (version->filename, map)' failed!
```

Сборка проекта

```sh
git clone https://github.com/Retera/WarsmashModEngine
cd WarsmashModEngine
chmod -v u+x gradlew
./gradlew desktop:runtime
```

Путь собранного проекта находится в ``WarsmashModEngine/desktop/build/image``

[Скачиваем игровые файлы - Английская версия](https://www.hiveworkshop.com/threads/wc3-download-archive-1-00-1-31-1-beta-demo.326836/)

Жмём на "Game Files" и скачиваем 1.29.2

```txt
Необходимы файлы
Копируем файлы

Иконки
[repo_name]/core/assets/resources

Скрипты и UI
[repo_name]/resources
```

Редактирование файла ``warsmash.ini`` для адаптации к версии 1.29.2

```txt
[DataSources]
Count=5
Type00=MPQ
Path00="../War3.mpq"
Type01=MPQ
Path01="../War3x.mpq"
Type02=MPQ
Path02="../War3xLocal.mpq"
Type03=Folder
Path03="resources"
Type04=Folder
Path04="../Maps"
```

```txt
[Emulator]
...
MaxPlayers=16 => 28
```

Включаем музыку ``EnableMusic=0 => 1``

Структура папок и файлов

```txt
 |-Warcraft III
 |  |-War3.mpq
 |  |-War3x.mpq
 |  |-War3xLocal.mpq
 |-warsmash
 |  |-core
 |  |  |-assets
 |  |  |  |-warsmash.ini
 |  |  |  |-resources
 |  |  |  |  |-Scrips
 |  |  |  |  |-UI
 |  |  |  |  |-Icon16.png
 |  |  |  |  |-Icon32.png
 |  |  |  |  |-Icon64.png
 |  |  |  |  |-Icon128.png
 |  |-desktop
 |  |-gradlew
 |  |-resources
```
