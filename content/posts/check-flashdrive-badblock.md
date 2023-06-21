+++
title = "Проверка usb флешки на плохие блоки (badblocks)"
date = 2023-03-04T20:38:06+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Источники:

- [Статья по использованию badblocks](https://www.cyberciti.biz/faq/linux-check-the-physical-health-of-a-usb-stick-flash-drive/)

Вводим для проверки

```bash
lslkb
```

```txt
sdc      8:32   1    15G  0 disk
└─sdc1   8:33   1    15G  0 part /run/media/anix/3C43483D50322F28
```

Для начало размонтируем раздел на флешке

```bash
umount /dev/sdc1
```

И вводим для проверки

```bash
sudo badblocks -w -s -o error.log /dev/sdc
```

- `-w` Используйте тест режима записи для сканирования плохих блоков путем записи некоторых шаблонов в каждый блок устройства, чтения каждого блока и сравнения содержимого.
- `-s` показывает прогресс сканирования
- `-o` выводит логи в файл error.log

Проверка заняла у меня долгое время (3 часа 16 минут) и это флешка на 16гб. Если в выходной файл error.log ничего не записано и в процессе показано что ошибок 0 (0/0/0 errors) значит флешка в порядке

Выхлоп команды

```txt
Testing with pattern 0xaa: done
Reading and comparing: done
Testing with pattern 0x55: done
Reading and comparing: done
Testing with pattern 0xff: done
Reading and comparing: done
Testing with pattern 0x00: done
Reading and comparing: done
```
