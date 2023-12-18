+++
title = "Полезное применение различных утилит Linux"
date = 2023-10-21T15:40:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

## systemd

### Редактирование unit systemd через nvim/vim

Вводите с добавлением опции -E

```sh
sudo -E systemctl edit reflector.timer
```

## hexedit, hexdump и xxd

- [Частично позаимствовано из данного видео гайда](https://www.youtube.com/watch?v=AbdkZZZ7-qA)

Сдампить все шестнадцатеричные числа и вывести в терминал убрав все точкии найти одинаковые паттерны байтов

Пример:

```txt
$ hexdump bea.exe -C | sed 's/\.//g' | grep 'AB AA AA 3F'
001db390  00 00 22 43 00 00 16 44  ab aa aa 3f 89 88 08 3b  |"CD?;|
001e4af0  ab aa aa 3f a0 8a 61 00  70 f6 52 00 a0 d0 52 00  |?apRR|
```

### Сравнивание и редактирование шестнадцатеричных (hex) чисел с двух разных бинарных файла

- [Источник](https://superuser.com/questions/125376/how-do-i-compare-binary-files-in-linux)

Конвертируем бинарники в читабельный формат, записывая в выходной файл которые будут удобны в сравнивании

```sh
xxd bea_mod.exe > bea_mod.hex
xxd bea.exe > bea.hex
```

Сравниваем с помощью neovim используя опцию -d (diff) и заодно редактируем тот который будет использоваться

```sh
nvim -d bea.hex bea_mod.hex
```

Как только сохранили .hex файл конвертируем его обратно в .exe используя в xxd опцию -r

```sh
xxd -r TMNT2.hex TMNT2.exe
```

DONE!

#### Только просмотр сравнивая оба

```sh
diff <(xxd [файл]) <(xxd [другой_файл]) > diff.txt
less diff.txt
```

### (Простой вид) Конвертирование 2-х бинарников в .txt вместе сравнивая и редактируя их

- [Идею позаимствовал из данного видео](https://www.youtube.com/watch?v=QU7CDAPoUiI)

Конвертируем в непрерывный формат вывода hex кода и меняем расширение на .txt

```sh
xxd -p bea.exe > bea.txt
xxd -p bea_mod.exe > bea_mod.txt
```

Редактором nvim сравниваем оба конвертированных файла

```sh
nvim -d bea.txt bea_mod.txt
```

Проводим манипуляции только с одним файлом (bea.txt)

После редактирование и его сохранение, обратно преобразуем этот модифицируемый текстовый файл в исполняемый .exe

```sh
xxd -r -p bea.txt bea.exe
```

### Как находить и редактировать по offset'у?

Используя hexdump (только показать)

```sh
hexdump TMNT2.exe -C | grep '00246450'
```

## vim/neovim

- [Видос по search and replace через vim](https://youtu.be/AZmEK1cjTgA?t=189)

Таймкод: 3:03-5:20

### Как добавить в конце всех строк обратную косую черту

- [Источник](https://stackoverflow.com/questions/594448/how-can-i-add-a-string-to-the-end-of-each-line-in-vim)

```txt
y desktop-file-utils
faudio
fontconfig
freetype2
gcc-libs
gettext
glu
lcms2
lib32-faudio
lib32-fontconfig
lib32-freetype2
lib32-gcc-libs
.....
```

Данная команда

```txt
:%norm A \
```

```txt
y desktop-file-utils \
faudio \
fontconfig \
freetype2 \
gcc-libs \
gettext \
glu \
lcms2 \
lib32-faudio \
lib32-fontconfig \
lib32-freetype2 \
.....
```

### Как слить строки блоком сниз в одну строку?

- [Источник](https://stackoverflow.com/questions/6577508/how-can-i-merge-multiple-lines-into-one-line-in-vim)

Команда

```txt
:%j
```

## wget

Скачивание файла и замена данного файла на существующий в root каталоге

Например эта целая строка

```sh
sudo wget -O /etc/makepkg.conf https://gitlab.com/anzix/dotfiles/-/raw/master/makepkg.conf
```

## sed

### Как при использовании sed не редактируя строку закомментировать её

- [Источник](https://unix.stackexchange.com/a/336774)

На примере убрав водяной знак в конфиге dgvoodoo.conf

```sh
sed -i '/^dgVoodooWatermark.*/ s/./;&/' dgVoodoo.conf
```

Раскомментирование

```sh
sed -i '/<pattern>/s/^#//g' file
```

Комментирование

```sh
sed -i "/<pattern>/s/^/#/g" file
# Пример
sed -i "/^#en_US.UTF-8 UTF-8/s/^#//g" /etc/locale.gen # раскомментировать
sed -i "/^en_US.UTF-8 UTF-8/s/^/#/g" /etc/locale.gen # закомментировать
```

Вставить строку рядом с другой

```sh
sed -i "/^PATTERN/s/WORD/WORD ADDED_WORD/" FILE
# Пример
sed -i "/^HOOKS=(.*)/s/udev/udev resume/" /etc/mkinitcpio.conf
```

Заменить целую строку если был найден паттерн

```sh
sed -i "/^PATTERN/c\REPLACEMENT" FILE
# Пример
sed -i "/^GRUB_DEFAULT=0/c\GRUB_DEFAULT=saved" /etc/default/grub
```

### Как с при использовании sed заменить значение строки с пробелами но не ломая оформление этой строки

- [Источник](https://stackoverflow.com/questions/44234364/replace-string-with-space-using-sed)

На примере данной строки

```txt
Filtering                           = appdriven
```

Вот так это выглядит

```sh
sed -i '/^Filtering/s/appdriven/trilinear/' dgVoodoo.conf
```

## Разное

```sh
# Список пакетов в одну большую строку
packages/base | xargs

# Список пакетов в колонку
packages/base | column -t
```
