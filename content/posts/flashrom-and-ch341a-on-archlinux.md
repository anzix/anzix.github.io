+++
title = "!Прошивальщик Flashrom+hexedit для ch341a на Arch Linux"
date = 2023-03-09T21:13:41+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux", "flashrom"]
+++

- [Инструкция от Mental Outlaw](https://www.youtube.com/watch?v=WyItt8FJWIs)

Скачиваем через pacman пакет flashrom

```sh
sudo pacman -S flashrom
```

В данном примере у меня модифицированный ch341a чтобы он работал на безопасные для чипа выходной вольтаж 3.3v. На стандартных 5 вольтах **!может!** привести к выгоранию платы, всё из-за чипа для которого операции для чтения и записи выполняются с конкретным диапазоном (например в даташит чипа Macronix MX25L8005 работа с 2.7 до 3.6 v)

![image](/images/flashrom-and-ch341a-on-archlinux/1660771428.png)

**<span style="color:red">!</span>Этот мод НЕ обязательно делать, это лишь моя рекомендация. Если у вас прямые руки и вы знаете что вы делаете<span style="color:red">!</span>**

Всё что нужно сделать так это отодвинуть вверх 28 пин на программаторе и припаять дорожку (у меня это отрезанный провод от пк спикера подогнанный под размер) к 3.3 вольт регулятору к ножке посередине

![image](/images/flashrom-and-ch341a-on-archlinux/IMG_20220825_153247.jpg)
![image](/images/flashrom-and-ch341a-on-archlinux/IMG_20220807_001408.jpg)
![image](/images/flashrom-and-ch341a-on-archlinux/CH341a-mod.jpg)

Кому интересно я оставлю ссылки на инструкции по моду ch341a

- [Текст инструкция 4PDA](https://4pda.to/forum/index.php?showtopic=884713&view=findpost&p=79548366)
- [Видеоинструкция 1](https://www.youtube.com/watch?v=HwnzzF645hA)
- [Видеоинструкция 2](https://www.youtube.com/watch?v=-ln3VIZKKaE)

Посмотреть что программатор подключен командой

```sh
lsusb
```

Вот так он у меня определился

```txt
Bus 001 Device 003: ID 1a86:5512 QinHeng Electronics CH341 in EPP/MEM/I2C mode, EPP/I2C adapter
```

Опции flashrom:

- `-p` программатор [название]
- `-r` читать
- `-w` записать (автоматический очищает информацию с чипа)

[Мануал по flashrom](https://linux.die.net/man/8/flashrom) (или просто ввести в терминале: `man flashrom`)
[Список поддерживаемых чипов программой flashrom](https://flashrom.org/Supported_hardware)

## Процедура прошивания чипа на примере MX25L8005

Это вынимаемый bios чип из материнской платы ASrock G41M-S rev 1.02.

![image](/images/flashrom-and-ch341a-on-archlinux/1660812885.png)

На чипе прошивка версии 1.50 мы её прошьём до версии 1.00 программой flashrom

![image](/images/flashrom-and-ch341a-on-archlinux/v1_50.jpg)

Проверяем поддержку данного чипа на [вики flashrom](https://www.flashrom.org/Supported_hardware), как видим всё ОК

![image](/images/flashrom-and-ch341a-on-archlinux/1660749562.png)

Вставляем чип в программатор 25 серия чтобы выемка (ключ) смотрела в сторону рукоятки. После чего подключаем в ПК

![image](/images/flashrom-and-ch341a-on-archlinux/image_2022-08-18_14-02-36.png)

Проверяем чип на чтение, flashrom сам определяет название чипа но если при чтении выдаёт ошибки вставьте доп флаг `-c <chipname>`

```sh
sudo flashrom -p ch341a_spi -r mx25l8005.bin
```

```txt
flashrom v1.2 on Linux 5.19.1-zen1-1-zen (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Found Macronix flash chip "MX25L8005/MX25L8006E/MX25L8008E/MX25V8005" (1024 kB, SPI) on ch341a_spi.
Reading flash... done.
```

Как видим всё прошло удачно

Чтобы проверить контрольные суммы CRC32 дампа биоса вводим

```sh
cksum [файл] # например mx25l8005.bin
```

```txt
3866296167 1048576 mx25l8005.bin
```

Сравнил таким образом контрольные суммы сдампленным AsProgrammer 1.4.1 из Windows 10 и могу с уверенностью сказать что суммы абсолютно идентичные

Чтобы просмотреть код сдампленный с flashrom необходим [hexedit](https://archlinux.org/packages/extra/x86_64/hexedit/)

```sh
sudo pacman -S hexedit
```

У утилиты нет Gui, всё будет показываться длинным списком в терминале

> Если хотите HEX редактор с интерфейсом:

- Gnone: `ghex`
- KDE: `okteta`

Вводим в терминале с флагом добавлением цветов в выбираем наш дамп файл

```sh
hexedit --color mx25l8005.bin
```

Навигация:

- F1 - для вызова помощи, открывает man страницу
- PageDown/PageUp - быстро скроллить список
- Ctrl+c - выйти без сохранения

![image](/images/flashrom-and-ch341a-on-archlinux/1660752136.png)

Начинаем записывать прошивку 1.00 в чип, flashrom сама стерёт информацию с нашего чипа и запишет выбранный нами файл затем проверит на схожесть

```sh
sudo flashrom -p ch341a_spi -w asrock\ g41m-s.BIN
```

```txt
flashrom v1.2 on Linux 5.19.1-zen1-1-zen (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Found Macronix flash chip "MX25L8005/MX25L8006E/MX25L8008E/MX25V8005" (1024 kB, SPI) on ch341a_spi.
Reading old flash chip contents... done.
Erasing and writing flash chip... Erase/write done.
Verifying flash... VERIFIED.
```

Если написано **VERIFIED** значит прошилось успешно

Считываем дамп для проверки

```sh
sudo flashrom -p ch341a_spi -r read1.bin
```

```txt
flashrom v1.2 on Linux 5.19.1-zen1-1-zen (x86_64)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Found Macronix flash chip "MX25L8005/MX25L8006E/MX25L8008E/MX25V8005" (1024 kB, SPI) on ch341a_spi.
Reading flash... done.
```

Можно проверить запись данных через hexedit

```sh
hexedit --color read1.bin
```

Считайте дамп 3 раза

```sh
sudo flashrom -p ch341a_spi -r read1.rom
sudo flashrom -p ch341a_spi -r read2.rom
sudo flashrom -p ch341a_spi -r read3.rom
# И проверяем суммы CRC32 или md5sum
md5sum read1.rom read2.rom read3.rom
```

Скачанный из remont-aud.net дамп биоса

```sh
cksum asrock\ g41m-s.BIN
```

```txt
2873525561 1048576 asrock g41m-s.BIN
```

Наш записанный прошивкой файл

```sh
cksum read1.bin
```

```txt
2873525561 1048576 read1.bin
```

Как мы видим всё совпадает, теперь вынимаем из программатора вставляем обратно в мать и проверяем на работоспособность, как видно ниже всё прошло удачно версия биоса понижена (дата фото запоздалое)

![image](/images/flashrom-and-ch341a-on-archlinux/v1_00.jpg)
