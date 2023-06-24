+++
title = "Декомпиляция apk инструментом apktool на Linux"
date = 2023-03-18T21:40:30+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Необходимо было декомпилировать у себя на своём устройстве файл framework-res.apk для того чтобы изменить строки связанные с WebView Bromite из-за которого я не смог заставить его работать

Качаем [Aur пакет](https://aur.archlinux.org/packages/android-apktool)

```sh
yay -S android-apktool
```

Копируем файл framework-res.apk на ПК и открываем консоль

- `apktool` - для вызова мануала

Декомпилируем файл

```sh
apktool d framework-res.apk
```

```
 ❯ apktool d framework-res.apk
Picked up _JAVA_OPTIONS: -Djava.util.prefs.userRoot=$HOME/.local/share/java
I: Using Apktool 2.6.1 on framework-res.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...
```
