+++
title = "F3 - Проверка карты памяти на фальшивый/поддельный объем"
date = 2023-03-02
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Некоторые поддельные карты рекламируют больший размер, чем их фактическая
мощность, что может привести к потере данных или другому неожиданному поведению.

С этим поможет утилита F3 (Fight Flash Fraud)

## Установка f3

Качаем данный инструмент

```bash
yay -S f3
```

[Документация и использование](https://github.com/AltraMayor/f3/blob/master/doc/usage.rst)

## Быстрая проверка

Для **быстрой** проверки не нужно монтировать устройство, просто выполняем данную
команду (может быть не надёжным)

> [!WARNING]
> Этот метод уничтожит ранее сохранённые данные

```bash
sudo f3probe --destructive --time-ops /dev/sdc
```

Вот выхлоп от данной команды

```sh
F3 probe 8.0
Copyright (C) 2010 Digirati Internet LTDA.
This is free software; see the source for copying conditions.

WARNING: Probing normally takes from a few seconds to 15 minutes, but
        it can take longer. Please be patient.

Good news: The device `/dev/sdc' is the real thing

Device geometry:
                *Usable* size: 116.08 GB (243445760 blocks)
               Announced size: 116.08 GB (243445760 blocks)
                       Module: 128.00 GB (2^37 Bytes)
       Approximate cache size: 0.00 Byte (0 blocks), need-reset=no
          Physical block size: 512.00 Byte (2^9 Bytes)

Probe time: 7'35"
Operation: total time / count = avg time
     Read: 1.32s / 4817 = 274us
    Write: 7'33" / 4192321 = 108us
    Reset: 1us / 1 = 1us
```

На 120 гб карты памяти сканирование заняло 7 с половиной минут

## Долгая проверка

TODO: Не проверено

Долгая проверка может показать надёжный и правильный результат, всё что она делает
это полностью заполняет объём устройства до тех пор пока оно не начнёт давать
признаки фальшивости. Вот как эту проверку выполнить

1. Монтируем устройство, например это у меня `/mnt/usb_flash`
2. Выполняем команду

   ```sh
   sudo f3write /mnt/usb_flash && f3read /mnt/usb_flash
   ```

   Если вдруг флешка отключилась на половине процесса то при повторной проверке
   укажите определённое число `<число>.h2w` на котором остановился `f3write` и
   продолжить проверку добавив аргумент `-s <число>` перед `f3write`

## Ссылки, источники

1. [Detecting Counterfeit Flash Drives On Linux](https://www.youtube.com/watch?v=NI3AGlhVpMM)
