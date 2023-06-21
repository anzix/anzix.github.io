+++
title = "!!Mpv и его настройка"
date = 2023-03-21T18:26:51+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Очень классный и минималистичный, а также кастомизируемый видеоплеер

* [Английский мануал по mpv здесь](https://mpv.io/manual/stable/)
* [Ответы на вопросы на англ](https://github.com/mpv-player/mpv/wiki/FAQ)

Скачаваем
```sh
sudo pacman -S mpv
```
Можно запустить тест который будет показывать за что отвечает та или иная клавиша если вы её нажмете в mpv

Введите в терминале
```sh
mpv --input-test --force-window --idle
```
1. В `mpv.conf`

```sh
# Сохранить позицию воспроизведение при выходе
# Сохранять в директорию
save-position-on-quit
watch-later-directory=~/.config/mpv/watch_later

#Зациклить видео с расширением webm
[extension.webm]
loop-file=inf
```
2. В input.conf

Как управлять громкостью колёсиком мыши?

Закинуть это в input.conf
```sh
AXIS_UP	add volume 3
AXIS_DOWN  add volume -3
```

Показать статистику видео

![](/images/mpv-and-setup/mpv_1.png)

* Нажмите i чтобы она на секунду появилась
* Нажмите Shift + i чтобы вкл на постоянку

## ff2mpv

* [Установка взята отсюда](https://github.com/woodruffw/ff2mpv/wiki/Installation-on-Linux)

Это полезное расширение для (Firefox, Chromium, Brave и т.д). Он позволяет легко запускать видео из браузера в mpv

Устанавливаем расширение на ваш браузер
* [Firefox](https://addons.mozilla.org/ru/firefox/addon/ff2mpv/)
* [Chromium](https://chrome.google.com/webstore/detail/ff2mpv/ephjcajbkgplkjmelpglennepbpmdpjg)

Клонируем репо и выполняем конечную установку для вашего браузера
```sh
git clone https://github.com/woodruffw/ff2mpv
cd ff2mpv
```
`./install.sh firefox` или `./install.sh chromium`

Готово! Расширение теперь работает

## Просмотр онлайн торренты с озвучкой и оформленными субтитрами вместе через mpv

Цель этого метода: сделать так чтобы можно было просматривать онлайн торренты с 100gb+ с полным комфортом (если ваш инет это позволяет). Также плюсом этого метода заключается в том что нет необходимости качать видеофрагменты на диск. Качать можно только Сабы и Озвучку на диск, что является жирным плюсом

* Первое что нужно сделать это скачать lua скрипт показанный в [этом разделе](http://localhost:1313/posts/mpv-and-setup/#mpv-webtorrent-hook)
* Далее идём на любой трекер, находим BDRemux или Blue-Ray раздачу с высоким разрешением, с отдельными дорожками и сабами
* Качаем только отдельно субтитры и озвучку
* И копируем magnet ссылку
* После открываем терминал и вводим схоже с показанным

> ВАЖНО: в `mpv.conf` должно быть прописано
```sh
# --Автоматически подхватывать папку с аудио дорожкой при совпадении с названием видеоматериала--
audio-file-auto=fuzzy

# --Автоматически подхватывать папку с субтитрами при совпадении с названием видеоматериала--
sub-auto=fuzzy
```
Вводим

```sh
mpv "magnet URL" -audio-file-paths="Путь к скачанной папке с озвучкой" -sub-file-paths="Путь к скачанной папке с субтитрами"
```
![](/images/mpv-and-setup/mpv-webtorrent-hook.png)

Как видно из этого output’а терминала (+) - значит что аудиодорожка и субтитры были добавлены

Если аудиодорожка не добавляется, попробуйте перекачать торрент с внешними дорожками и сабами

### Специальные оформленные шрифты

До

![](/images/mpv-and-setup/before.png)

После

![](/images/mpv-and-setup/after.png)

Они обычно вот так поставляются в торрент раздачах

![](/images/mpv-and-setup/fonts.png)

Создаёте папку fonts (соблюдая регистр)
```sh
mkdir ~/.config/mpv/fonts
```
И закидываем всё из папки раздачи **Fonts** в `~/.config/mpv/fonts`
Проверяем, всё должно сработать как показано на скринах До и После


> К сожалению в mpv пока что нет похожей реализации как -audio-file-paths и -sub-file-paths, сурс
>
> НО есть возможность с этим lua скриптом и версий mpv которая поддерживает опцию sub-fonts-dir (поддерживается только одна директория), если запустить на обычной версии mpv вылезает ошибка о несовместимости
>
> `[sub_fonts_dir_auto] This version of mpv does not support the sub-fonts-dir option`
>
> Возможно скоро появится такая опция

## Самые полезные Lua скрипты для MPV

Эти скрипты очень продвинут наш плеер, делая его более функциональным

Создайте папку **scripts**
```sh
cd ~/.config/mpv
mkdir scripts
```
В папку **scripts** мы будем помещать наши скачанные lua скрипты

После установки скрипта, проверьте его работоспособность в терминале

Введите mpv [путь к видео] и жмите enter, и чекайте терминал

### mpv_thumbnail_script.lua
* https://github.com/marzzzello/mpv_thumbnail_script/releases

Самый полезный

Отображает миниатюры на видео дорожке при перемещении курсора

Установка:

Скачиваем эти два файла и кидаем их в **scripts**

* `mpv_thumbnail_script_client_osc.lua`
* `mpv_thumbnail_script_server.lua`

Также необходимо в `mpv.conf` перевести пункт osc (ибо как на пикриле скрипт будет жаловаться)
```sh
osc=no
```
![](/images/mpv-and-setup/mpv_thumbnail_warning.png)

#### Ускорение процесса
Если у вас мощный проц и он многопоточный, то мы можем сделать наш скрипт ещё быстрее.

Сделайте 2 `mpv_thumbnail_script_server` но с цифрой в конце

![](/images/mpv-and-setup/speedup-process-thumbnails-1.png)

Так мы увеличим скорость обработки миниатюр в видео

Готово!

![](/images/mpv-and-setup/speedup-process-thumbnails-2.png)

### fuzzydir.lua

* https://github.com/sibwaf/mpv-scripts#fuzzydirlua

Рекурсивный поиск путей звуковых дорожек и субтитров вместо ручного их прописывания путей для каждой папки

Если вам надоело постоянно указывать директорию внешних аудиодорожек и субтитров внутри которой присутствует множество разных, в конфиг файле `mpv.conf` это можно указать данными опциями
```sh
audio-file-paths="Sounds/AniLibria:Sounds/Crunchyroll:Sounds/Студийная Банда"
sub-file-paths="Subtitles/Anime Chap [ENG]:Subtitles/Crunchyroll:Subtitles/RikuSempaiStuff:Subtitles/YakuSub Studio"
```
Однако так лишь загромождает конфиг файл, и вводить это утомительно.

С данным скриптом закинутым в папку **scripts** можно просто добавить /** в конце
```sh
audio-file-paths="Sounds/**"
sub-file-paths="Subtitles/**"
```
И всё что в папке Sounds рекурсивно будет добавлено в ваш плеер


### mpv-webtorrent-hook

* https://github.com/noctuid/mpv-webtorrent-hook

Позволяет таскать URL magnet прямо в mpv и воспроизводить их, плюсом также можно таскать .torrent файл прямо в mpv и воспроизводить его не скачивая. Внешние сабы и озвучка не подхватываются этим скриптом, только встроенные сабы и озвучка. Поэтому аниме не посмотреть

Установка

Качаем необходимые пакеты
```sh
yay -S webtorrent-cli xidel
sudo pacman -S jq
```
Клонируем репо в папку **scripts**
```sh
cd scripts
git clone https://github.com/noctuid/mpv-webtorrent-hook.git
```
Запускаю mpv

Копирую и таскаю URL magnet (или .torrent) прЯмо в окно mpv

РАБОТАЕТ!!!

> * Бывает что при повторе mpv вылетает, хз с чем это связано но после ребута всё норм стало (либо не скачаны пакеты)
> * Также в этом скрипте можно добавить свои опции создав конфиг файл, для подробностями [сюда](https://github.com/noctuid/mpv-webtorrent-hook#configuration)

#### Смена директории кэша торрент файлов
Кэш автоматом будет удалятся когда плеер с webtorrent’ом открыт поэтому волноваться не стоит

Создаём конфиг файл и вставляем (я вписал свой hdd диск и всё в порядке)
```sh
nvim ~/.config/mpv/script-opts/webtorrent-hook.conf
```
```sh
# change download directory
download_directory=/media/webtorrentcache
```

### sponsorblock.lua

* https://github.com/po5/mpv_sponsorblock/blob/master/sponsorblock.lua

![](/images/mpv-and-setup/sponsorblock-lua.png)

Всеми любимое расширение для браузеров по блокировке спонсор блоков теперь в mpv, только вот работает оно только только при новом воспроизведении

Клонируем репо sponsorblock, вытаскиваем папку **sponsorblock_shared** и скрипт **sponsorblock.lua** переместив их в папку **scripts**

SponsorBlock работает как через терминал `mpv <URL>` так и простым перетаскиванием видео напрямую открыв mpv

### youtube-quality.lua

* https://github.com/jgreco/mpv-youtube-quality

Можно выбирать качество youtube через mpv на `ctrl+f`

Установка:

* [Закидываем скрипт](https://raw.githubusercontent.com/jgreco/mpv-youtube-quality/master/youtube-quality.lua) внутрь `~/.config/mpv/scripts/`
* Редактируя скрипт заменяем на 164 строке с youtube-dl на yt-dlp по `[совету от данного реддитора](https://www.reddit.com/r/mpv/comments/sy497o/comment/hxvry3t/?utm_source=share&utm_medium=web2x&context=3) чтобы скрипт работал.

### autoload.lua

* https://github.com/mpv-player/mpv/blob/master/TOOLS/lua/autoload.lua

Позволяет запускать вместо одного медиафайла а разом несколько на очереди в текущей папке,

Поддерживает:
* Аудио
* Видео
* Картинки (фиг знает зачем)

В данном конфиге я убрал автозагрузку изображений отредактировав скрипт закоментировав данные строки

![](/images/mpv-and-setup/autoload-lua.png)

### playlistmanager.lua

* https://github.com/jonniek/mpv-playlistmanager/blob/master/playlistmanager.lua

Управлять плейлистом, воспроизводя треки из списка. Очень удобен при дополнительном использовании с [mpv-webtorrent-hook](https://github.com/noctuid/mpv-webtorrent-hook)

Просто копируем lua конфиг в **scripts**

Редактируем `input.conf` и вставляем для активации менеджера
```sh
# Бинды для playlistmanager.lua
TAB script-binding showplaylist
```
Нажимает TAB и управляем стрелками

### reload.lua

* https://github.com/4e6/mpv-reload/blob/master/reload.lua

Когда онлайн-видео застряло во время буферизации или замедленного источника CDN, перезагрузка часто помогает. Этот скрипт обеспечивает автоматическую перезагрузку видео, у которых не было прогресса буфера для некоторого времени, сохраняя текущую позицию времени. Он также добавляет бинд Ctrl + r для перезагрузки видео вручную.


### pause-indicator.lua

* https://gist.github.com/torque/9dbc69543118347d2e5f43239a7e609a

Всплывает индикатор паузы и воспроизведения по середине, как в ютубе

![](/images/mpv-and-setup/pause-indicator.png)
