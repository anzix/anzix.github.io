+++
title = "Как слить пустой неразмеченный раздел Btrfs в уже существующий точку монтирования root"
date = 2023-03-08T19:37:24+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- [Первая инструкция](https://ask.fedoraproject.org/t/how-add-more-space-in-a-btrfs-filesystem-how-add-a-new-partition-how-add-a-new-disk-linux/14817)
- [Вторая инструкция](https://stackoverflow.com/questions/71315723/resize-my-btrfs-filesystem-to-add-the-free-unallocated-space)

Что странно я нашёл такой способ но он отличается от того что предоставляет ext4

```sh
df
```

```txt
/dev/sda6        btrfs      42G          18G   24G           44% /
/dev/sda6        btrfs      42G          18G   24G           44% /.snapshots
/dev/sda6        btrfs      42G          18G   24G           44% /home
```

Скачал Gparted для удобства

```sh
sudo pacman -S gparted
```

Сжимаем из того размера раздел который необходим, затем форматируем форматируем как "очищенный" (создался как /dev/sda8)

Далее открываем терминал и вводим

```sh
sudo btrfs device add /dev/sda8 /home
```

Готово теперь раздел увеличен с 24гб до 100гб

Чтобы проверить размер использования вводим

```sh
btrfs filesystem usage /home
```

```txt
WARNING: cannot read detailed chunk info, per-device usage will not be shown, run as root
Overall:
    Device size:		 100.01GiB
    Device allocated:		  17.56GiB
    Device unallocated:		  82.45GiB
    Device missing:		     0.00B
    Used:			  16.56GiB
    Free (estimated):		  83.02GiB	(min: 41.80GiB)
    Free (statfs, df):		  83.02GiB
    Data ratio:			      1.00
    Metadata ratio:		      2.00
    Global reserve:		  53.03MiB	(used: 0.00B)
    Multiple profiles:		        no

Data,single: Size:16.00GiB, Used:15.43GiB (96.41%)

Metadata,DUP: Size:768.00MiB, Used:581.70MiB (75.74%)

System,DUP: Size:32.00MiB, Used:16.00KiB (0.05%)
```

Чтобы удалить слитый раздел вводим

```txt
sudo btrfs device remove /dev/sda8 /home
```

Проверяем

```sh
df
```

```txt
/dev/sda6        btrfs      108G          20G   88G           19% /
/dev/sda6        btrfs      108G          20G   88G           19% /.snapshots
/dev/sda6        btrfs      108G          20G   88G           19% /home
```
