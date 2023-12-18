+++
title = "Google Chrome или Chromium в Arch Linux"
date = 2023-03-12T17:10:47+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

<span style="color:red">Внимание</span>: Я не рекомендую пользоваться Гугл Хромом на Арче по нескольким причинам:

- Возникают мелкие графические артефакты, в определённых местах, пока не знаю с чем это связано (пикча)
- Возникают проблемы с открытием “Настройка и управление Chrome”, которая совсем не открывается, фиксится сменой раб. столов

Поэтому как замену google-chrome <span style="color:green">рекомендуется использовать</span> chromium или ungoogled-chromium

![image](/images/google-chrome-and-chromium-on-archlinux/google-chrome-bug.png)

Необходимо установить шрифты для корректной работы данных браузеров

```sh
sudo pacman -S ttf-dejavu noto-fonts noto-fonts-emoji
```

Устанавливаем на выбор браузер

```sh
# AUR пакет Google Chrome
yay -S google-chrome
# Стандартный Chromium
sudo pacman -S chromium
```

## Включение Аппаратного Декодирования (Hardware Decode)

- [Arch Wiki - Configure VA-API](https://wiki.archlinux.org/title/Hardware_video_acceleration#Configuring_VA-API)

Для проверки наличия VA-API

```sh
vainfo
```

При такой ошибке необходим пакет [`libva-mesa-driver`](https://archlinux.org/packages/extra/x86_64/libva-mesa-driver/)

```txt
vaInitialize failed with error code -1 (unknown libva error),exit
```

Для проверки наличия VDPAU

```sh
vdpauinfo
```

При такой ошибки необходим пакет [`mesa-vdpau`](https://archlinux.org/packages/extra/x86_64/mesa-vdpau/)

```txt
display: :0 screen: 0 Failed to open VDPAU backend libvdpau_radeonsi.so: cannot open shared object file: No such file or directory Error creating VDPAU device: 1
```

**Скачиваем эти пакеты для AMD**

```sh
sudo pacman -S libva-mesa-driver mesa-vdpau
```

Также **очень рекомендуется** установить расширение [enhanced-h264ify](https://chrome.google.com/webstore/detail/enhanced-h264ify/omkfmpieigblcllmkgbflkikinpkodlk) чтобы принудительно ставить аппаратный рендер видео в H264 вместо vp8/vp9/AV1 (без него в консоли показывается что аппаратный видеодекодер отключен)

С включёнными флагами описанными ниже удалось заставить google chrome и chromium/ungoogled-chromium работать на аппаратном декодировании

Включаем любое видео в ютубе, жмём F12 - три точки - More tools - Media. Далее жмём на вкладку Media и видим название видоса в списке, жмём на него и видим всю инфу о декодировании, в данном случае работает VDAVideoDecoder

![image](/images/google-chrome-and-chromium-on-archlinux/hardware-encoding-enabled.png)

### Необходимые и полезные флаги

<span style="color:green">Все флаги которые у вас включены в браузер</span>: chrome://version/

Повзаимствованые флаги

- https://github.com/dreammjow/ChromiumHardening
- https://github.com/GoogleChrome/chrome-launcher/blob/main/docs/chrome-flags-for-tools.md

Настройка флагов для подобных браузеров идентична, кроме разве что названия конфига

**Для Chromium / Ungoogled-Chromium**: Вводим параметры конфига по данному пути `~/.config/chromium-flags.conf`

**Для Google Chrome (НЕ ТЕСТИРОВАЛ)**: Вводим параметры конфига по данному пути `~/.config/chrome-flags.conf`

**Для Brave**: Вводим параметры конфига по данному пути
`~/.config/brave-flags.conf`

1. <span style="color:green">Описание</span>:

- WebUIDarkMode - Включает тёмную тему
- ScrollableTabStrip - (**<span style="color:green">Рекомендуется</span>** для Arch Linux) Отключает скролл вкладок
- DownloadBubble - Новый вид загрузок как в Edge

`--enable-features=WebUIDarkMode,ScrollableTabStrip,DownloadBubble`

2. <span style="color:green">Описание</span>: Принудительная тёмная тема браузера

`--force-dark-mode`

3. <span style="color:green">Описание</span>: Убираем уведомление "Сделать хром браузером по умолчанию"

`--no-default-browser-check`

4. <span style="color:green">Описание</span>: Отключить автоматическое добавление поисковых систем с веб-страниц

`--disable-search-engine-collection`

5. <span style="color:green">Описание</span>: Включает параллельную закачку для ускорение скорости скачивания

`--enable-parallel-downloading`

6. <span style="color:green">Описание</span>: Убирает панель "Список для чтения"

`--hide-sidepanel-button`

7. <span style="color:green">Описание</span>: Убирает в newtab самые посещаемые сайты

`--disable-top-sites`

8. <span style="color:green">Описание</span>: Отключает ненужную иконку профиля

`--show-avatar-button=never`

9. <span style="color:green">Описание</span>: Автоматический отклоняет любые уведомления разрешения браузера

`--deny-permission-prompts`

10. <span style="color:green">Описание</span>: Восстанавливает последнюю сессию

`--restore-last-session`

11. <span style="color:green">Описание</span>: Улучшаем производительность путём включения Hardware Acceleration (Аппаратное ускорение)

```sh
--ignore-gpu-blocklist
--enable-gpu-rasterization
--enable-zero-copy
--use-vulkan
```

12. <span style="color:green">Описание</span>: Включаем 144hz, вставляете любую т.к обе команды одинаково действуют и работают

На выбор:

1. VA-API для xwayland (wayland)

`--use-gl=desktop`

2. VA-API для Xorg

`--use-gl=egl`

13. <span style="color:green">Описание</span>: Дополнительно прописываем вот это, чтобы отключить сообщения о ошибках

`--disable-gpu-driver-bug-workarounds`

**UPD (17.03.22)**: Обнаружил такую <span style="color:green">проблему</span> при обновленном google-chrome/chromium

```txt
Problems Detected
Accelerated video decode/encode has been disabled, either via blocklist, about:flags or the command line.
Disabled Features: video_decode/video_encode
```

<span style="color:green">Решение</span>: Вводим в \*-flags.conf внутри данной секции

```sh
--enable-features=....VaapiVideoEncoder,VaapiVideoDecoder,CanvasOopRasterization..
--disable-features=UseChromeOSDirectVideoDecoder
```

Проверить на поддержку Hardware Acceleration, введите `chrome://gpu`

![image](/images/google-chrome-and-chromium-on-archlinux/fully-hw-accelerated.png)

**UPD (11.06.22)**: Следующая ошибка при обновлённом chromium'e/chrome

```txt
Problems Detected
WebGPU has been disabled via blocklist or the command line.
Disabled Features: webgpu
```

Это экспериментальная опция, может нарушить работу браузера. Но чуть увеличивает плавность рендера 3D объектов

`--enable-unsafe-webgpu`

<span style="color:green">Описание</span>: Браузер будет подхватывать pac прокси от antizapret (не используя расширения)

`--proxy-pac-url=https://antizapret.prostovpn.org/proxy.pac`

- [Тестирование браузера на 144hz](https://testufo.com/)
- [Тестирование на 3D объекты 1](https://solarsystem.nasa.gov/planets/saturn/overview/)
- [Тестирование на 3D объекты 2](https://webglsamples.org/aquarium/aquarium.html)
- [Тестирование на аппаратное ускорение](https://madebyevan.com/webgl-water/)

**Совет**: Raw Draw игнорьте и не включайте, будут проблемы в отображениях шрифтов

### Решаем проблему медленного скролла мыши Chromium Based браузеров (Исправлено в 109.0.5414.74)

Это не проблема Linux а проблема браузеров на основе electron/chromium об этом упоминается в [Arch Wiki + способы решения](https://wiki.archlinux.org/title/chromium#Chromium_slow_scroll_speed)

Самым простым решением будет использовать расширение [SmoothScroll](https://chrome.google.com/webstore/detail/smoothscroll/nbokbjkabcmbfdlbddjidfmibcpneigj)

Есть вариант с использование инструмента [imwheel](https://archlinux.org/packages/extra/x86_64/imwheel/) который умеет настраивать ход колеса мыши к любому софту. Однако работает оно с некоторыми раздражающими <span style="color:red">ПРОБЛЕМАМИ</span>.

- [imwheel на Arch Wiki](https://wiki.archlinux.org/title/IMWheel)
- [Видео гайд от DT](https://www.youtube.com/watch?v=NoHfqfRYbk4)
- [Тоже оч годный гайд (большую часть почерпнул от него)](https://www.youtube.com/watch?v=jFcpCsrIpYk)

Устанавливаем пакет

```sh
sudo pacman -S imwheel
```

Создаём конфиг в $HOME директории и вставляем

```sh
nvim .imwheelrc
```

```sh
"^(chromium|google-chrome|brave-browser)$"
	# Фикс scroll
	None, Up, Button4, 2.8
	None, Down, Button5, 2.8
	# Без изменений для zoom'а страниц
	Control_L, Up,   Control_L|Button4
	Control_L, Down, Control_L|Button5

# vim:ts=4:shiftwidth=4:syntax=sh
```

Для меня нормальное значение скролла будет 2.8, вы же можете играться с этими значениями если вас не устраивает мои

Далее для теста запустим данный инструмент командой

```sh
imwheel -b "45" -k
```

**Описание опций**:

- `-b "45"` - боковые кнопки мыши не будут затрагиваться при запуске этой команды, без этой опции боковые вообще не будут работать
- `-k` - перезагружает процесс imwheel

Если видите такой выхлоп в терминале значит инструмент запущен

```txt
INFO: imwheel started (pid=6604)
```

Убить данный процесс командой

```sh
pkill imwheel
```

Для автозапуска imwheel при запуске системы прописываем в автозапуск файла `~/.xprofile` (Для DM) или `~/.xinitrc` (запуск с TTY)

```sh
# Исправление скролла мыши
imwheel -b "45"
```

### Установка удалённых расширений

Папка с расширениями находится по такому пути

**Chrome**: `~/.config/google-chrome/Default/Extensions`

**Chromium**: `~/.config/chromium/Default/Extensions`

Для примера я установлю удалённый из магазина расширений chrome, open source торрент поисковик

- [TMS (Torrents MultiSearch)](https://github.com/Feverqwe/tSearch)

[Скачиваем](https://github.com/Feverqwe/tSearch/files/6478194/ngcldkkokhibdmeamidppdknbhegmhdh.zip) данное расширение в формате zip

Открываем окно расширения chrome - "Загрузить распакованное расширение". И открываем папку с распакованным расширением, имя которого начинается с **ngcldk...**

У вас в списке появится данное расширение
