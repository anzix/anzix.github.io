+++
title = "!Миграция с X11 i3wm на Wayland Sway"
date = 2023-06-21T15:50:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Очень полезная статья с актуальными и полезными советами с инструкциями по wayland sway

```sh
sudo pacman -S sway waybar wofi mako swaybg wl-clipboard slurp grim
```

- `sway` - эквивалентно i3
- `waybar` - эквивалентно polybar
- `wofi` - эквивалентно rofi
- `wl-clipboard` - чтобы копировать (yank) текст с nvim и вставлять его, эквивалентно xclip
- `slurp` - эквивалентно maim
- `grim` - скриншот утилита для wayland
- `otf-font-awesome` - шрифты для waybar и swappy

```sh
mkdir ~/.config/sway
cp ~/.config/i3/config ~/.config/sway/
```

> Примечание: действия связанные с данным конфигом применяться только сочетанием клавиш **win+shift+c** и смене раб. стола

Редактируем конфиг файл

```sh
nvim ~/.config/sway/config
```

## Заставлять юзать для приложений и игр xwayland

```config
xwayland force

# Убрать title bar
default_border pixel 2
default_floating_border pixel 2
```

## Настройка клавиатуры

```sh
swaymsg -t get_inputs
```

```conf
input "type:keyboard" {
    # Смена раскладки
    xkb_layout us,ru
    xkb_options grp:caps_toggle,grp_led:caps,grp:switch

    # Ускорение печатания
    repeat_delay 210
    repeat_rate 40
}
```

## Отключение акселерации на мышке

```sh
swaymsg -t get_inputs
```

```conf
input "1133:49284:Logitech_G102_Prodigy_Gaming_Mouse" {
	accel_profile "flat"
}
```

## Обои статично

```sh
sudo pacman -S swaybg
```

```conf
output "*" bg "/media/Media/Галерея/Обои на раб.стол/Ramen.jpg" fill
```

## Включение 144hz

```sh
swaymsg -t get_outputs
```

```conf
output DP-1 mode 1920x1080@144Hz
```

`adaptive_sync on` - включает VRR (Variable Refresh Rate) Переменную частоту кадров
При включении лично у меня возникали мерцания при движении мышки, поэтому я убрал VRR

[Сурс проблемы](https://github.com/swaywm/sway/issues/5076)

## Wofi - запуск программ подобно rofi

```sh
sudo pacman -S wofi
```

```conf
bindsym $mod+d exec wofi --show drun --allow-images
```

## waybar - как polybar но для sway

```sh
sudo pacman -S waybar
```

```conf
bar swaybar_command waybar
```

## Запуск программ и скриптов, подобно i3wm

Прописываем в конфиг файл

```sh
exec {
	autotiling
	/usr/lib/polkit-gnome/polkit-gnome-authentication-agent-1
	mako
	mpd
	mpDris2
	udiskie -t
	corectrl
	steam -silent
	gammastep-indicator
	otd
# Запускает xwayland игры на основной монитор (если несколько мониторов), также исправляет работу мыши на играх wine
	xrandr --output XWAYLAND0 --primary
# Фикс GTK3 Темы
~/.config/sway/import-gsettings
# Load Xrdb
xrdb -merge $HOME/.config/X11/Xresources
}
```

Добавил

```conf
include /etc/sway/config.d/*
```

Убрал т.к в sway перезапускать конфиг только win+shift+c

```conf
bindsym $mod+Shift+r restart
```

## FIX GTK3 Темы

- [Источник](https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland)

Создаём файл `~/.config/sway/import-gsettings`, и вставляем

```sh
#!/bin/sh

# Взято: https://github.com/swaywm/sway/wiki/GTK-3-settings-on-Wayland

# usage: import-gsettings
config="${XDG_CONFIG_HOME:-$HOME/.config}/gtk-3.0/settings.ini"
if [ ! -f "$config" ]; then exit 1; fi

gnome_schema="org.gnome.desktop.interface"
gtk_theme="$(grep 'gtk-theme-name' "$config" | sed 's/.*\s*=\s*//')"
icon_theme="$(grep 'gtk-icon-theme-name' "$config" | sed 's/.*\s*=\s*//')"
cursor_theme="$(grep 'gtk-cursor-theme-name' "$config" | sed 's/.*\s*=\s*//')"
font_name="$(grep 'gtk-font-name' "$config" | sed 's/.*\s*=\s*//')"
cursor_size="$(grep 'gtk-cursor-theme-size' "$config" | sed 's/.*\s*=\s*//')"
gsettings set "$gnome_schema" gtk-theme "$gtk_theme"
gsettings set "$gnome_schema" icon-theme "$icon_theme"
gsettings set "$gnome_schema" cursor-theme "$cursor_theme"
gsettings set "$gnome_schema" font-name "$font_name"
gsettings set "$gnome_schema" cursor-size "$cursor_size"
gsettings set "$gnome_schema" color-scheme prefer-dark
```

```sh
chmod +x import-gsettings
```

И добавляем в `~/.config/sway/config`

```conf
exec_always {
	...
	# Фикс GTK3 Темы
    ~/.config/sway/import-gsettings
	...
}
```

Ребутимся и после всё иконки, темы и курсор применились

Тема курсора не применилась до конца, решается это данной настройкой

```sh
sudo pacman -S seatd
```

И добавляем в `~/.config/sway/config`

```conf
seat * xcursor_theme capitaine-cursors 16
```

Перезагружаем конфиг используя `win+shift+c`

Убирать простаивающий курсор через 3 сек

```conf
seat * hide_cursor 3000
```

Можно отключить тайлинг drag mouse

```conf
# Disable dragging of tiles with a mouse.
tiling_drag disable
```

## Включаем Sharing Screen OBS на Sway

- [Инструкция](https://www.youtube.com/watch?v=mo8GV7QIWDc)

```sh
sudo pacman -S pipewire xdg-desktop-portal-wlr qt5-wayland slurp wofi
```

После того как добавили захват записи перед вами откроется wofi с выбором дисплея, если у вас установлен `slurp` тогда у вас появится курсор ввиде перекрестье в котором достаточно кликнуть на дисплей для захвата

## Форк redshift для wayland - gammastep

- [GitLab Page](https://gitlab.com/chinstrap/gammastep)

```sh
sudo pacman -S gammastep
mkdir ~/.config/gammastep
```

Вставляем данные `~/.config/gammastep/config.ini`

```conf
[general]
location-provider=manual
adjustment-method=wayland
[manual]
lat=[ваше значение долготы]
lon=[ваше значение широты]
```

Запуск `gammastep-indicator &`

Сайты с нахождением координат широты и долготы

- [Первый](http://www.geonames.org/) предложенный Arch Wiki
- [Второй](https://www.latlong.net/) более удобный

## Clipboard Manager для sway - cliphist (Ввиде Rofi)

![](/images/i3wm-to-sway-migration/cliphist.png)

- [GitHub страница](https://github.com/sentriz/cliphist)

Имеет возможность хранить картинки в буфере, но нету (или ещё не добавили) возможность игнорировать буфер Менеджеров паролей keepassxc

Также может работать как и с `dmenu` так и с `wofi`, правда работа в dmenu отображается горизонтально

Качаем [Aur пакет](https://aur.archlinux.org/packages/cliphist)

```sh
yay -S cliphist
```

Пакет не смог собраться

```txt
==> Проверка файлов source с использованием sha256sums...
    cliphist-0.3.1.tar.gz ... СБОЙ
==> ОШИБКА: Исходные файлы не прошли проверку целостности!
 -> ошибка сборки: cliphist
```

Пришлось качать [бинарный файл cliphist](https://aur.archlinux.org/packages/cliphist-bin) который установился успешно

```sh
yay -S cliphist-bin
```

Вставляем в конфиг `~/.config/gammastep/config.ini` для бинд клавиш и автозапуска

```ini
exec {
    ...
    # Запуск менеджер буфера обмена
    exec wl-paste --watch cliphist store
    ...
}

# Показать clipboard (для копирования)
bindsym $mod+v exec cliphist list \
  | wofi -d \
  | cliphist decode \
  | wl-copy

# Показать clipboard (для удаления)
bindsym $mod+Alt+Bar exec cliphist list \
   | wofi -d \
   | cliphist delete
```

## Скриншон экрана на Sway

```sh
mkdir ~/Pictures/Screenshots/
nvim ~/.config/sway/config/
```

```conf
# --- Screenshots --- #
# Requres: slurp grim swappy wl-copy
set $scr-dir ~/Pictures/Screenshots

# Full Screenshot, без буфера обмена
bindsym Print exec --no-startup-id \
  IMG=$scr-dir/$(date +%s).png \
  && grim $scr-dir/$(date +%s).png \
  && notify-send -i $IMG \
  "Скриншот сохранён" "Место: $IMG" || notify-send "Failed to take screenshot"


# Скрин с выделением области или клик на окно в фокусе + буфер обмена
# Если вам не нужен буфер обмена уберите строку с wl-copy
bindsym $mod+Shift+s exec --no-startup-id \
  IMG=$scr-dir/$(date +%s).png \
  && swaymsg -t get_tree \
	| jq -r '.. | select(.pid? and .visible?) | .rect | "\(.x),\(.y) \(.width)x\(.height)"' \
	| slurp \
	| grim -t png -g - "$IMG" \
  && wl-copy -t image/png < $IMG \
  && notify-send -i $IMG "Обрезанный скриншот сохранён в буфер обмена" "Место: $IMG" || notify-send "Failed to take screenshot"


# Сканер палитры (color picker)
# https://github.com/swaywm/sway/wiki/Tricks#html-color-picker
# bindsym $mod+Shift+d exec grim -g "$(slurp -p)" -t ppm - | convert - -format '%[pixel:p{0,0}]' txt:- | tail -n 1 | cut -d ' ' -f 4 | wl-copy
bindsym $mod+Shift+p exec --no-startup-id \
	notify-send "Color Picker" "Выберите цвет" && \
    grim -g "$(slurp -p)" -t ppm /tmp/color-picker.png && \
    convert -size 100x100 xc:"$(convert /tmp/color-picker.png -format '#%[hex:s]\n' info:)" /tmp/color-picker.png && \
    notify-send -i /tmp/color-picker.png "Палитра скопирована в буфер обмена" $(convert /tmp/color-picker.png -format "#%[hex:s]\n" info:) && \
    convert /tmp/color-picker.png -format "#%[hex:s]\n" info: | wl-copy -n



# OCR сканер
# Требуется: tesseract tesseract-data-rus tesseract-data-eng tesseract-data-jpn wl-copy slurp
bindsym $mod+Shift+f exec --no-startup-id slurp \
  | xargs -I {} grim -s 2 -t png -g {} - \
  | tesseract -l rus+eng+jpn - - \
  | wl-copy -n



# QR код сканер
# Требуется: slurp grim zbar wl-copy
# bindsym Shift+Print exec grim -g "$(slurp -d)" - | zbarimg -q --raw PNG:- | wl-copy
bindsym Shift+Print exec \
	wl-copy $(slurp | grim -g - - | zbarimg -q --raw -) \
	&& qr=$(wl-paste) \
	&& notify-send "QR-code" "QR-code successfully read and copied to clipboard: <i>$qr</i>" -t 6000


# Быстрый перевод на Русский
# Требуется: tesseract tesseract-data-rus tesseract-data-eng tesseract-data-jpn translate-shell slurp grim wl-copy
bindsym alt+t exec --no-startup-id \
	ocr-translate-sway
```

Загрузить скриншот в pastebin 0x0.st и скопировать ссылку в буфер обмена

Вставляем содержимое в скрипт файл `~/.local/bin/ocr-translate-sway`

```sh
#!/bin/bash
IMAGE_NAME=/tmp/_.png
FILE_NAME=/tmp/_.txt
FILE_TMP=/tmp/__.txt
x=$IMAGE_NAME && touch "$x" && slurp | grim -g- "$x"
tesseract -l eng+jpn --dpi 200 $IMAGE_NAME $(echo $FILE_NAME | awk '{split($0, a, "."); print a[1]}')
sed '/^[[:space:]]*$/d' $FILE_NAME > $FILE_TMP
cat $FILE_TMP # | wl-copy -n # Для доступа к оригинальному скопированному тексту
translation=$(trans -b "$(cat $FILE_TMP)")
echo -n $translation | wl-copy -n # Для доступа к переведённому тексту
notify-send "$translation"
rm $IMAGE_NAME $FILE_NAME $FILE_TMP
```

Для бинда вставляем в конфиг `~/.config/gammastep/config.ini`

```conf
# Upload screenshot and copy url to clipboard
bindsym $mod+Shift+Print exec slurp | grim -g - - | curl --form 'file=@-' http://0x0.st | wl-copy && swaynag -m "screenshot uploaded & url copied to clipboard
```

## Скриншот с возможностью на лету быстро редактировать используя swappy

Устанавливаем необходимые пакеты

```sh
sudo pacman -S slurp grim swappy wl-copy
```

Использование

```sh
grim -g "$(slurp)" - | swappy -f -
```

По дефолту swappy сохраняет в $HOME/Desktop, чтобы изменить хранение создаём папку swappy и config внутри неё с добавлением содержимого

```sh
mkdir ~/.config/swappy/config
nvim ~/.config/swappy/config
```

```conf
[Default]
save_dir=$HOME/Pictures/Screenshots
save_filename_format=swappy-%Y%m%d-%H%M%S.png
show_panel=false
line_size=5
text_size=20
text_font=sans-serif
```

## Mako - wayland альтернатива dunst

```sh
sudo pacman -S mako libnotify
mkdir ~/.config/mako/config
```

[Пример конфига](https://github.com/emersion/mako/wiki/Example-configuration)

Все опции: `man 5 mako`

Вставляем в конфиг файл `nvim ~/.config/mako/config`

```conf
sort=-time
layer=overlay
background-color=#11121D
width=500
height=110
border-size=2
border-color=#88c0d0
icons=1
max-icon-size=70
default-timeout=5000
ignore-timeout=1
font=JetBrains Mono Nerd Font Medium 10

[urgency=low]
border-color=#d08770
text-color=#A0A8CD

[urgency=normal]
border-color=#d08770
text-color=#A0A8CD

[urgency=high]
border-color=#EE6D85
text-color=#EE6D85
default-timeout=0

[category=mpd]
default-timeout=2000
group-by=category
```

Вставляем для автозапуска в конфиг sway `~/.config/sway/config`

```conf
exec {
	....
	mako
	....
}

# История уведомлений Mako win+`
bindsym $mod+grave exec makoctl restore
```

Перезапуск mako после применения изменений конфига

```sh
makoctl reload
```

Тестирование (также работает и с dunstify)

```sh
notify-send -u low "lol" "kek"
notify-send -u normal "lol" "kek"
notify-send -u critical "lol" "kek"
```

## !Трей pasystray для sway (wayland) Не пробовал

```sh
yay -S pasystray-wayland
```

## !Запускать всё программы через прослойку xwayland

Это исправляет проблемы steam с DISPLAY

```sh
sudo pacman -S xorg-xwayland
```

## !Правка Chromium флагов для wayland

Редактируем конфиг файл `~/.config/chromium-flags.conf`

```conf
# Исправляет дерганный скролл
--ozone-platform-hint=auto

# Позволяет делится экраном (для zoom и discord)
--enable-features=...WebRTCPipeWireCapturer...
```

Непонятно для чего этот пакет, вроде необходим для sharing screen

```sh
sudo pacman -S libpipewire02
```

## Показ клавиш альтернатива Screenkey на wayland - wshowkeys

- [GitHub страница](https://github.com/ammgws/wshowkeys)
- [Обзор от Brodie Robertson](https://www.youtube.com/watch?v=DGTN_vHAEIE)

Качаем [AUR пакет](https://aur.archlinux.org/packages/wshowkeys-git)

```sh
yay -S wshowkeys-git
```

И вставляем в sway конфиг `~/.config/sway/config`

```conf
# Вкл/Выкл режим показа клавиш снизу посередине
bindsym $mod+p exec "killall wshowkeys || wshowkeys -a bottom -t 1"
```

## !!Альтернатива xprop для sway и как это использовать

Самый неочевидный способ

```sh
swaymsg -t get_tree | less
```

И через / искать app_id и вставлять в for_window

Метод чуть по проще

```sh
swaymsg -t get_tree | jq '.' | grep app_id
```

## Запуск sway

Изменил параметры автозапуска sway в `.zprofile` чтобы при каждом открытии терминала не вылезал ещё один sway поверх sway'я. Для начало экспортируя необходиммые переменные для работы wayland

```sh
nvim ~/.zprofile
```

```conf
# Необходимые переменные wayland
set_wayland_env(){
	export MOZ_ENABLE_WAYLAND=1
    export XDG_SESSION_TYPE=wayland
    export XDG_CURRENT_DESKTOP=sway
	export QT_WAYLAND_DISABLE_WINDOWDECORATION=1 # hide window decoratins in older versions of QT
	export NO_AT_BRIDGE=1 # Подавляет предупреждение о accessibility bus в GTK
	export SDL_VIDEODRIVER=wayland # Позволяет запускать SDL2 игры на wayland
	export QT_QPA_PLATFORM="wayland;xcb" # Позволяет запускать QT приложения на wayland
	export _JAVA_AWT_WM_NONREPARENTING=1 # Исправляет ошибки отрисовки приложений Java jre8
	export MOZ_WEBRENDER=1 # Enable WebRender for Firefox
}


# Запуск sway
[ -z $DISPLAY ] && [ $(tty) = /dev/tty1 ] && set_wayland_env && exec sway # --verbose >> ~/.sway.log
```

## Решение проблем sway

- Проблема: Уведомление Dunst при игре застрял курсор мышки

Решил это отредактировав конфиг dunst принудительно работать dunst на xwayland

```sh
nvim ~/.config/dunst/dunstrc
```

Меняем c **false** на **true**

```conf
force_xwayland = true
```

- Проблема: При запуске любого видео из браузера или через mpv звук не появляется и браузер застревает но если открыть pavucontrol тогда звук появится

У меня установлен pipewire wireplumber

Проблема решается если в терминале ввести `wireplumber`
