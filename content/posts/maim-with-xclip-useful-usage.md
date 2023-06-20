+++
title = "Минималистичный скриншотер (связка) maim+xclip"
date = 2023-03-22T19:03:32+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++
Если вам не очень понравился flameshot то связка из 2 мелких пакетов может сделать ваш день с Linux лучше

Можно скринить игры при помощи опции `-o`

Все опции описаны в `man page maim`
```sh
sudo pacman -S xclip maim
```

**Цвет выделения**

<font color="red">Не рекомендуется</font> вместе с юзать с опцией `-o` (Disable graphics acceleration), да и вообще если делаете скрин игры

Светло синее
```sh
maim -lc 0.3,0.4,0.6,0.4
```
Фиолетовое
```sh
maim -lc 0.3,0,0.5,0.3
```
Светло белое
```sh
maim -lc 1,1,1,0.3
```
И добавляем в бинды [sxhkd](https://github.com/baskerville/sxhkd) ниже описанное

> Если сохраняете скриншоты в HDD NTFS диск, тогда советую не ставить date по типу $(date +%F-%T).png (Имя файла будет 2022-04-15-22:56:12.png) т.к файловая система ntfs не позволяет создавать файлы в которых содержат ” * / : < > ? \ | а на шинде вы не сможете просмотреть эти скрины
>
> Ставьте $(date +%s) и тогда проблем с файловой системой ntfs не возникнут

```sh
# Скрин всего экрана, без clipboard
Print
    IMG=~/Pictures/Screenshots/$(date +%s).png \
    && maim $IMG \
    && notify-send -i $IMG \
    "Скриншот сохранён" "Место: $IMG" || notify-send "Failed to take screenshot"


# Скрин с выделением или нажатием на выбранное окно
super + shift + s
    PIC=~/Pictures/Screenshots/$(date +%s).png \
    && maim -suof png $PIC \
    && cat $PIC | xclip -selection clipboard -t image/png \
    && notify-send -i $PIC \
    "Обрезанный скриншот сохранён в буфер обмена" "Место: $PIC" || notify-send "Failed to take screenshot"

# Скрин окна который в фокусе (для тех у кого нету мышки)
super + shift + P maim ~/Pictures/Screenshots/$(date +%s).png -ui \
    $(xdotool getactivewindow) \
    && notify-send -i ~/Pictures/Screenshots/$(date +%s).png \
    "Фокус скриншота сохранён" "Место: ~/Pictures/Screenshots \nИмя: $(date +%s).png" || notify-send "Failed to take screenshot"
```

Или через i3 конфиг файл

```sh
# Скрин всего экрана, без clipboard
bindsym Print exec --no-startup-id \
 IMG=~/Pictures/Screenshots/$(date +%s).png \
 && maim $IMG \
 && notify-send -i $IMG \
 "Скриншот сохранён" "Место: $IMG" || notify-send "Failed to take screenshot"

# Скрин с выделением области или клик на выбранное окно
bindsym $mod+shift+s exec --no-startup-id \
 PIC=~/Pictures/Screenshots/$(date +%s).png \
 && maim -suof png $PIC \
 && cat $PIC | xclip -selection clipboard -t image/png \
 && notify-send -i $PIC \
 "Обрезанный скриншот сохранён в буфер обмена" "Место: $PIC" || notify-send "Failed to take screenshot"


# Скрин окна который в фокусе (для тех у кого нету мышки)
bindsym $mod+shift+P exec --no-startup-id \
 maim ~/Pictures/Screenshots/$(date +%s).png -ui \
 $(xdotool getactivewindow) \
 && notify-send -i ~/Pictures/Screenshots/$(date +%s).png \
 "Фокус скриншота сохранён" "Место: ~/Picturesg/Screenshots \nИмя: $(date +%s).png" || notify-send "Failed to take screenshot"
```

## Делаем из maim сканнер цветовой палитры

![](/images/maim-with-xclip-useful-usage/colorpicker.png)

Качаем пакет если у вас его нету
```sh
sudo pacman -S slop
```

И добавляем в бинды [sxhkd](https://github.com/baskerville/sxhkd) ниже описанное
```sh
# Определение цветовой палитры
super + shift + d
    notify-send "Color Picker" "Выберите цвет" && \
    maim /tmp/color-picker.png -ug $(slop -t 0) && \
    convert -size 100x100 xc:"$(convert /tmp/color-picker.png -format '#%[hex:s]\n' info:)" /tmp/color-picker.png && \
    notify-send -i /tmp/color-picker.png "Палитра скопирована в буфер обмена" $(convert /tmp/color-picker.png -format "#%[hex:s]\n" info:) && \
    convert /tmp/color-picker.png -format "#%[hex:s]\n" info: | xclip -sel c
```
Или через i3 конфиг файл
```sh
bindsym $mod+shift+d exec notify-send "Color Picker" "Выберите цвет" && maim /tmp/color-picker.png -ug $(slop -t 0) && convert -size 100x100 xc:"$(convert /tmp/color-picker.png -format '#%[hex:s]\n' info:)" /tmp/color-picker.png && notify-send -i /tmp/color-picker.png "Палитра скопирована в буфер обмена" $(convert /tmp/color-picker.png -format "#%[hex:s]\n" info:) && convert /tmp/color-picker.png -format "#%[hex:s]\n" info: | xclip -sel c
```

## !Делаем из maim OCR сканер с помощью Tesseract + скрипта

Устанавливаем пакеты
```sh
sudo pacman -S tesseract tesseract-data-rus tesseract-data-eng tesseract-data-jpn
```
[Скачиваем скрипт](https://github.com/demo2k20/dots/blob/c391251f9f00fc2ce3414508c485c3af309dc6fb/.local/bin/ocr) делаем его исполняемым (`chmod +x ocr`) и закидываем его в $PATH переменную (т.е в `~/.local/bin`)


В скрипте **необходимо** выставить доп язык для более <font color="green">лучшего распознавания</font>. OCR сканер Tesseract умеет работать с нескольколькими языками, 3 языка вместе работают хорошо, поэтому дерзайте.

Редактируя скрипт в 13 строке, плюсуем любой из скачанных языковых пакетов tesseract-data-[язык]
```sh
tesseract -l [язык]+[язык]+[язык]
```
![](/images/maim-with-xclip-useful-usage/ocr.png)

Не забываем что для доп распознавания необходимо скачать пакет с данным языком иначе результат будет неудовлетворительным

Список доступных языков Tesseract
```sh
tesseract --list-langs
```
Далее теперь можно забиндить скрипт на любую клавишу

Через [sxhkd](https://github.com/baskerville/sxhkd)
```sh
# OCR сканер maim + tesseract + xclip
super + shift + f
    ocr
```
Через i3/config
```sh
bindsym $mod+shift+f exec ocr
```

## Делаем из maim сканер QR кодов ZBar + Xclip

Исходник скрипта:
* https://github.com/jayden-chan/dotfiles/blob/5b6d5b6ad820be4d491779cfdba4c68d90699882/scripts/screenshot.sh

Для сканирования необходим пакет zbar
```sh
sudo pacman -S zbar
```
Сохраняем мой модифицированный скрипт в $PATH переменную (т.е в `~/.local/bin`) для удобного запуска

И биндим на любую удобную клавишу

Через [sxhkd](https://github.com/baskerville/sxhkd)
```sh
# QR сканер maim + zbar + xclip
super + shift + Print
    qr-scan
```
## !Делаем из maim переводчик текста с помощью Tesseract + Translate-Shell + Xclip

Источник скрипта:
* https://github.com/jkovacevic/dotfiles/blob/master/shared/bash/ocr.sh

При выделении Японского или Английского текста translate-shell переведёт его на Русский скопирует в буфер обмена и выведет уведомление переведённого

Можете отредактировать данный скрипт и добавить другие языки которые вы хотите перевести

Для работы необходимо установить пакет translate-shell и база данных языков для tesseract
```sh
sudo pacman -S translate-shell tesseract-data-eng tesseract-data-jpn
```
[Сохраняем мой модифицированный скрипт](https://gitlab.com/anzix/dotfiles/-/raw/master/local/.local/bin/scripts/ocr-translate) в $PATH переменную (т.е в `~/.local/bin`) для удобного запуска

И биндим на любую удобную клавишу

Через [sxhkd](https://github.com/baskerville/sxhkd)
```sh
# Быстрый перевод на Русский (tesseract + translate-shell + xclip)
alt + r
 	ocr-translate
```

