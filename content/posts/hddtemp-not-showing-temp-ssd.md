+++
title = "Hddtemp не показывает температуру SSD"
date = 2023-10-08T20:10:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux"]
+++

- [Источник](https://askubuntu.com/questions/474669/ssd-temperature-sensor-readout-with-hddtemp)
- [Arch Wiki](https://wiki.archlinux.org/title/Hddtemp#Solid_State_Drives)

Установка утилиты

```sh
sudo pacman -S hddtemp
```

Выхлоп

```txt
# hddtemp /dev/sda
ВНИМАНИЕ: Диск /dev/sda не дает показания температурного датчика.
ВНИМАНИЕ: Это еще не значит, что у него его нет.
ВНИМАНИЕ: Если вы точно знаете, что датчик есть, напишите hddtemp@guzu.net
ВНИМАНИЕ: (см. опции --help, --debug и --drivebase).
/dev/sda: Samsung SSD 860 EVO 250G B          	@:  нет датчика
```

Так как информация о температуре ssd находится в другом поле (а именно поле 190)  необходимо править датабазу hddtemp ручками

Этой командой я добавляю обнаружение температуры своего SSD полем 190

```sh
sudo bash -c 'cat << EOF >> /etc/hddtemp.db
"Samsung SSD 860 EVO 250G" 190 C "Samsung SSD 860 EVO 250G"
EOF'
```
