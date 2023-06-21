+++
title = "Минималистичные просмотрщики изображений"
date = 2023-03-24T00:48:14+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

Ниже представлены разные просмотрщиков основное их отличие это комплексность

## feh

- [ENG Гайд по настройке с подробностями](https://uriesk.wordpress.com/2017/04/30/using-feh-as-the-default-image-viewerbrowser-in-a-tiling-wm-environment/)
- [Список опции в Arch Wiki](https://man.archlinux.org/man/feh.1#OPTIONS)

Самый легковесный просмотрщик изображений. Его ещё можно юзать как обои на раб стол

```sh
sudo pacman -S feh
```

- `~/.config/feh/themes` - для настройки фона и тем
- `~/.config/feh/keys` - для клавиатурных биндов
- `~/.config/feh/buttons` - для биндов кнопок мыши

### Скролл изображений мышкой

- [Источник из subreddit](https://www.reddit.com/r/linuxquestions/comments/ae3pzr/how_to_get_feh_to_zoom_in_with_scroll_wheel/)

Вставляем в `~/.config/feh/buttons`

```sh
# Unbind existing scroll operations
prev_img
next_img

# Set <action> <mouse button>
zoom_in 4
zoom_out 5
```

### Изменение фона и автомасштабирование

- [Источник из stackexchange](https://unix.stackexchange.com/questions/426526/how-to-permanently-set-default-color-of-fehs-background-to-black)

Меня постоянно бесит прозрачность на фоне, хорошо что данный фон можно изменить (я поставил чёрный) в конфигурационном файле `~/.config/feh/themes` добавил данные строки

```sh
feh --image-bg=black \
--draw-filename \
--scale-down \
--auto-zoom \
....
```

### Исправление шрифтов кириллицы в пути папок и названии

Качаем шрифт, например roboto

```sh
sudo pacman -S ttf-roboto
```

Вставляем к дополнению к конфигу `~/.config/feh/themes`

```sh
....
--fontpath /usr/share/fonts/TTF \
--font Roboto-Regular/11 \
--menu-font Roboto-Regular/11 \
....
```

### Действия (action)

- [Источник](https://unix.stackexchange.com/questions/227863/bind-key-to-custom-script-in-feh)

Данные действия значительно упрощаю работу с картинками, как собственно и с nsxiv

> Использование их осуществляется только цифрами на клавиатуре
>
> Чтобы показать список действий нажмите `a`

Вставляем к дополнению к конфигу `~/.config/feh/themes`

```sh
--action1 ";[Скопировать изображение в буфер обмена] xclip -selection clipboard -t image/png < %F && dunstify 'Изображение скопировано!' '%F'" \
--action2 ";[Скопировать путь в буфер обмена] echo %F | xclip -i -selection clipboard && dunstify 'Путь к файлу скопирован' '%F'"  \
--action3 ";[Отредактировать используя Gimp] gimp %F &" \
--action4 ";[Показать в проводнике pcmanfm] pcmanfm %F/.. &"
```

## nsxiv

[Simple X Image Viewer](https://github.com/muennich/sxiv) из семейства [Suckless](https://suckless.org/) который заброшен и не развивается. Поэтому второй ([Neo Simple X Image Viewer](https://codeberg.org/nsxiv/nsxiv)) является его форком т.е продолжением который поддерживаться и пока распространяется в виде [AUR пакета](https://aur.archlinux.org/packages/nsxiv)

- [Обзор на sxiv от Люка](https://www.youtube.com/watch?v=GYW9i_u5PYs)
- [Обзор на nsxiv от Brodie](https://www.youtube.com/watch?v=ocwuA9Cs8RA)

Установка

```sh
yay -S nsxiv
```

> 80% в этом просмотрщике управляется клавой (20% управляется мышкой), поэтому я рекомендую посетить man страницу по нему если у вас возникли вопросы `man nsxiv`

Коротко о стандартных хоткеях и командах

```sh
# Следующая | предыдущая картинка
      n, Пробел | p, Backspace
# Или мышкой кликая с края изображения

# (клавой) Приблизить | Отдалить
          +|-

# (мышкой) Приблизить | Отдалить
Колёсиком мыши

# (клавой) Перемещение при зуме
# Стрелки | vim клавиши
    ←↓ ↑ → | h,j,k,l

# (Мышкой) Перемещение при зуме
# Удерживая СКМ

# Перевернуть картинку на 90º
# Вправо | Влево
     Shift+>|Shift+<

# (Через фм) Воспроизвести | остановить Gif (если гифка статичная)
Ctrl+Space

# (Через терминал) Воспроизвести Gif командой
nsxiv -a [гифка].gif

# Отметить одно изображение (Выделить)
m
```

### Изменяем цвет задника, передник, шрифт и цвет бара а также Метку

Он меняется в `.Xresources` такими параметрами

```sh
Nsxiv.window.background: #000000
Nsxiv.window.foreground: #e7e7e7
Nsxiv.bar.background: #000000
Nsxiv.mark.foreground: #8DF345
Nsxiv.bar.font: monospace-12
```

### Обработчик-клавиш (key-handler)

Это очень сильно расширяет функционал nsxiv добавляя хоткей клавишу **Ctrl+x** пробуждая наш обработчик-клавиш который потом считывает в файле определённую клавишу, которая определена в нём

Создаём папку

```sh
mkdir -pv ~/.config/nsxiv/exec/
```

И копируем key-handler как у [Luke Smith](https://github.com/LukeSmithxyz/voidrice/blob/master/.config/sxiv/exec/key-handler) в указанную директорию

Делаем его исполняемым

```sh
chmod +x key-handler
```

При открытии картинки жмём комбинацию клавиш **Crtl+x** и указанную в key-handler клавишу

### Больше информации в Status bar

Данный конфиг добавляет внизу

[Размер] [ширина/высота] [Имя файла] [Zoom] [картинка по счёту]

Копируем файл, делаем его исполняемым и проверяем

```sh
cp /usr/share/doc/nsxiv/examples/image-info ~/.config/nsxiv/exec/
```

Открывать все изображения находящиеся в каталоге. А не по одному, особенно если юзаете pcmanfm

#### !В виде скрипта

[Качаете скрипт nsxiv-rifle](https://codeberg.org/nsxiv/nsxiv-extra/raw/branch/master/scripts/nsxiv-rifle/nsxiv-rifle) и закидываете его в $PATH переменную (т.е в `~/.local/bin`), в терминале вводим данную команду

```sh
wget -P $HOME/.local/bin https://codeberg.org/nsxiv/nsxiv-extra/raw/branch/master/scripts/nsxiv-rifle/nsxiv-rifle
```

Либо можете взять (немного модифицированный) скрипт из [моего dotfiles](https://gitlab.com/anzix/dotfiles/-/raw/master/base/local/.local/bin/scripts/nsxiv-rifle)

Делаем его исполняемым

```sh
chmod +x nsxiv-rifle
```

Создаём **nsxiv-rifle.desktop** в директории `$HOME/.local/share/applications/`

Вписываем всё это и сохраняем

```sh
nvim ~/.local/share/applications/nsxiv-rifle.desktop
```

```sh
[Desktop Entry]
Type=Application
Name=nsxiv-rifle
GenericName=Image Viewer
Exec=nsxiv-rifle %F
MimeType=image/bmp;image/gif;image/jpeg;image/jpg;image/png;image/tiff;image/x-bmp;image/x-portable-anymap;image/x-portable-bitmap;image/x-portable-graymap;image/x-tga;image/x-xpixmap;image/webp;
NoDisplay=true
Icon=nsxiv
```

Далее прописываем в `$HOME/.config/mimeapps.list` и сохраняем

```sh
nvim ~/.config/mimeapps.list
```

```sh
image/jpg=nsxiv-rifle.desktop;
image/jpeg=nsxiv-rifle.desktop;
image/png=nsxiv-rifle.desktop;
image/gif=nsxiv-rifle.desktop;
image/webp=nsxiv-rifle.desktop;
image/tiff=nsxiv-rifle.desktop;
image/bmp=nsxiv-rifle.desktop;
```

И теперь вместо одного изображения в каталоге будут все открываться в pcmanfm и других GUI файловых менеджерах

#### !Патчить source code nsxiv

Появилась возможность [пропатчить nsxiv](https://codeberg.org/nsxiv/nsxiv-extra/src/branch/master/patches/load-dir)
