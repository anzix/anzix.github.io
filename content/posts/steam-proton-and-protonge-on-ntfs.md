+++
title = "Работа Steam игр с использованием Proton и Proton GE на NTFS разделе"
date = 2023-03-17T16:05:06+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

- [Инструкция в Wiki Proton](https://github.com/ValveSoftware/Proton/wiki/Using-a-NTFS-disk-with-Linux-and-Windows)

В первую очередь обновил fstab раздел Games

```sh
nvim /etc/fstab
```

```txt
UUID=30C4C35EC4C32546 /media/Games ntfs-3g rw,nofail,errors=remount-ro,noatime,prealloc,fmask=0022,dmask=0022,uid=1000,gid=984,windows_names 0 0
```

Основные опции:

- `fmask=0022`
- `dmask=0022`
- `uid=1000`
- `gid=984`
- `windows_names`

Затем сделал символьную ссылку папки **compatdata**, которая используется для префиксов proton/wine в которых есть имена файлов с : двоеточием, файловая система NTFS которая не понимает

```sh
mkdir -p ~/.steam/steam/steamapps/compatdata
ln -svi ~/.steam/steam/steamapps/compatdata /media/Games/SteamLibrary/steamapps/
```

После чего игры начали сразу работать
