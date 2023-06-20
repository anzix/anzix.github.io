+++
title = "Проверка состояния HDD и SSD через терминал"
date = 2023-03-10T21:52:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

- [Arch Wiki - S.M.A.R.T](https://wiki.archlinux.org/title/S.M.A.R.T.)
- [opennet](https://www.opennet.ru/man.shtml?topic=smartctl&category=8&russian=2)

Скачиваем пакет smartmontools

```sh
sudo pacman -S smartmontools
```

Пояснение по опциям:

- `-i` / `-info` печатает различную информацию о устройстве, включая использование S.M.A.R.T, версии прошивок, версии SATA, Скорость SATA и т.д
- `-A` / `-attributes` печатает только атрибуты S.M.A.R.T. [Подробное описание что означает каждое имя атрибут](https://www.ixbt.com/storage/hdd-smart-testing.shtml#n7)
- `-H` / `--health` попросить устройство сообщить о своем состоянии SMART или ожидающих сообщениях TapeAlert. Проще говоря это быстрый тест здоровья HDD или SSD

Запускаем от sudo вывод простой инфы о нашем ssd диске

```sh
sudo smartctl -i /dev/sda
```

У меня такая информация о моём SSD

```txt
=== START OF INFORMATION SECTION ===
Model Family:     	Samsung based SSDs
Device Model:     	Samsung SSD 860 EVO 250GB
Serial Number:    	S3Y9NX0M351690Y
LU WWN Device Id:	5 002538 e40dcdd86
Firmware Version: 	RVT04B6Q
User Capacity:    	250 059 350 016 bytes [250 GB]
Sector Size:      	512 bytes logical/physical
Rotation Rate:    	Solid State Device
Form Factor:      	2.5 inches
TRIM Command:     	Available, deterministic, zeroed
Device is:        	In smartctl database [for details use: -P show]
ATA Version is:   	ACS-4 T13/BSR INCITS 529 revision 5
SATA Version is:  	SATA 3.2, 6.0 Gb/s (current: 6.0 Gb/s)
Local Time is:    	Mon Nov 22 07:33:23 2021 +05
SMART support is: 	Available - device has SMART capability.
SMART support is: 	Enabled
```

Вывод атрибут S.M.A.R.T

```sh
sudo smartctl -A /dev/sda
```

Output атрибуты моего SSD выглядит так

![](/images/s.m.a.r.t-control-hdd-ssd/attributes.png)

- [Взял инфу отсюда](https://www.altlinux.org/%D0%9F%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0_%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F_%D0%B6%D0%B5%D1%81%D1%82%D0%BA%D0%B8%D1%85_%D0%B4%D0%B8%D1%81%D0%BA%D0%BE%D0%B2)

Ключевое значение имеет содержимое 3-х столбцов:

- `VALUE` - текущее значение параметра
- `WORST` - наихудшее значение, которого когда-либо достигало значение Value
- `THRESH` - значение, которого должен достигнуть Value этого же атрибута, чтобы состояние атрибута было признано критическим.

Критичным является поле `WHEN_FAILED`, если оно имеет значение `FAIL`, то высока вероятность выхода жесткого диска из строя в ближайшее время.

Т.е если значение `VALUE` ближе к `THRESH`, тем ближе кончина диска

Запускаем от sudo быстрый тест здоровья HDD или SSD

```sh
sudo smartctl -H /dev/sda
```

```txt
=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED
```

Если написано PASSED значит диск в порядке. Если результат не PASSED, то диск следует заменить.

## GUI программа для бенчмарка SSD/HDD

KDiskMark является полной копией CrystalDiskMark

```sh
sudo pacman -S kdiskmark
```

![](/images/s.m.a.r.t-control-hdd-ssd/kdiskmark.png)

## Cli проверка на скорость чтения диска

Устанавливаем утилиту hdparm

```sh
sudo pacman -S hdparm
```

[О hdparm](https://losst.ru/kak-ostanovit-zhesnkij-disk-v-linux)

Опции:

- `-T` - выполняет тест скорости чтения из кэша;
- `-t` - выполняет тест скорости чтения с диска без кэширования;

Запускаем проверку

```sh
sudo hdparm -Tt /dev/sd[a|b|c]
```

У меня на моём HDD 1TB

```txt
/dev/sdb:
Timing cached reads: 25236 MB in 1.99 seconds = 12681.60 MB/sec
Timing buffered disk reads: 614 MB in 3.00 seconds = 204.59 MB/sec
```

На SSD Samsung Evo 860 256GB

```txt
/dev/sda:
Timing cached reads: 24594 MB in 1.99 seconds = 12357.06 MB/sec
Timing buffered disk reads: 1484 MB in 3.00 seconds = 494.21 MB/sec
```
