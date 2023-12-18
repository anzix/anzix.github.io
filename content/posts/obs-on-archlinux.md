+++
title = "!OBS на Arch Linux"
date = 2023-03-10T00:20:38+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- [Arch Wiki OBS](https://wiki.archlinux.org/title/Open_Broadcaster_Software)

## Способы установки obs на Arch

### Установка Flatpak версии OBS

- [Flathub страница Obs](https://flathub.org/apps/details/com.obsproject.Studio)
- [Видео обзор](https://www.youtube.com/watch?v=FPjEdrik-eQ)

Как по мне самый рекомендуемый способ, после установки всё сразу идёт из коробки

- [Установка Flatpak на Arch Linux]({{< ref "/content/posts/flatpak.md" >}})

Установка Flatpak версии obs:

```sh
flatpak install flathub com.obsproject.Studio
```

Запуск через консоль:

```sh
flatpak run com.obsproject.Studio
```

### Нативная установка

Установив обычный пакет в офф репо Arch вы не получаете весь функционал OBS а лишь обрезанную его часть. Кому-то это больше нравится, однако obs полностью юзабелен и требует лишь некоторой доработки, для тех кто привык использовать весь функционал obs рекомендую лучше придерживаться установки Flatpak версии.

```sh
sudo pacman -S obs-studio
```

## obs-vkcapture

- https://github.com/nowrep/obs-vkcapture
- https://wiki.archlinux.org/title/Open_Broadcaster_Software#Capturing_via_Vulkan/OpenGL

В целом вы можете записывать и без данной приблуды

> Вопрос: Для чего нужен vkcapture?
>
> Ответ: Он необходим для захвата игр напрямую через непосредственно API Vulkan или OpenGL который в итоге выжимает ещё больше дополнительной производительности с записи или потока делая всю эту работу менее ресурсоёмкой.

Требуется версия Obs 27.0 или новее

Есть 2 типа установки на выбор:

Установка для [нативного obs](http://localhost:1313/posts/obs-on-archlinux/#%D0%BD%D0%B0%D1%82%D0%B8%D0%B2%D0%BD%D0%B0%D1%8F-%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0)

- <span style="color:green">РЕКОМЕНДУЕТСЯ</span>: [obs-vkcapture-git](https://aur.archlinux.org/packages/obs-vkcapture-git) ([lib32-obs-vkcapture-git](https://aur.archlinux.org/packages/lib32-obs-vkcapture-git)) собирает AUR пакеты из последнего коммита git т.е является экспериментальной версией
- [obs-vkcapture](https://aur.archlinux.org/packages/obs-vkcapture) ([lib32-obs-vkcapture](https://aur.archlinux.org/packages/lib32-obs-vkcapture)) собирает AUR пакеты из стабильной релизной ветки, их ставить не надо

> Примечание: [lib32-obs-vkcapture-git](https://aur.archlinux.org/packages/lib32-obs-vkcapture-git) необходим для того чтобы захватывать 32bit'ные старые игры по типу (Worms Armageddon и т.д)

Установка для [Flatpak OBS](http://localhost:1313/posts/obs-on-archlinux/#%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-flatpak-%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B8-obs)

```sh
flatpak install com.obsproject.Studio.Plugin.OBSVkCapture org.freedesktop.Platform.VulkanLayer.OBSVkCapture
```

### Использование obs-vkcapture

В Lutris для отдельной игры открываем Настройки - Параметры системы - ищем "Префикс команды" и вписываем либо одно, либо другое в зависимости от вашей игры.

```sh
# Для OpenGL или Vulkan API
obs-gamecapture
# Только для Vulkan API
obs-vkcapture
```

Сохраняем и запускаем

Для использования в Steam просто прописываем в Параметры запуска игры:

```sh
... obs-gamecapture ... %command%
```

**Для Xorg (X11)**: Запускаем Obs используя команду в терминале: `OBS_USE_EGL=1 obs`

Теперь после запуска у нас в obs появится "Захват игры" и оставляем "Захватывать любое окно". После можно проверить запись игры или начать тестовую трансляцию.

![image](/images/obs-on-archlinux/obs-vkcapture.png)

## !Стриминг и запись OBS плагином кодировщиком GStreamer VAAPI для Arch Linux

- [Github страница gstreamer-vaapi](https://github.com/GStreamer/gstreamer-vaapi)
- [Arch Wiki Gstreamer-Vaapi](https://wiki.archlinux.org/title/GStreamer#Hardware_video_acceleration)
- [Arch Wiki OBS кодировщик GStreamer](https://wiki.archlinux.org/title/Open_Broadcaster_Software#Encoding_using_GStreamer)
- [Легкая инструкция на Reddit для Arch + Flatpak](https://www.reddit.com/r/linux_gaming/comments/w4i3qf/easy_way_to_get_good_4k_60fps_obs_encoding/)
- [Видеоинструкция](https://www.youtube.com/watch?v=RYH15sMwjUg)

[Видео-тестирование кодировщика GStreamer-Vaapi](https://www.youtube.com/watch?v=OWouopxGueQ) записанный [GloriousEggroll](https://github.com/GloriousEggroll) (разраб Proton-GE), утверждающий что больше не нужен кодировщик AMF из проприетарных драйверов AMDGPU-PRO. Показывая что кодировщик GStreamer Vaapi в разы лучше AMF и позволяет записывать аж в 4K 60 FPS 60k битрейтом и это всё без перегрузов.

Работает как и на RDNA 2 так и для старых RDNA 1.

Плюсы:

- Не требуется плагин [obs-streamfx](https://aur.archlinux.org/packages/obs-streamfx)

Для установки на нативный OBS необходим сам [кодировщик из офф репо Арч](https://archlinux.org/packages/extra/x86_64/gstreamer-vaapi/) и [плагин из AUR](https://aur.archlinux.org/packages/obs-gstreamer) который будет встроен в obs.

```sh
yay -S gstreamer-vaapi obs-gstreamer
```

Для установки для [Flatpak OBS](http://localhost:1313/posts/obs-on-archlinux/#%D1%83%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-flatpak-%D0%B2%D0%B5%D1%80%D1%81%D0%B8%D0%B8-obs):

```sh
flatpak install com.obsproject.Studio.Plugin.Gstreamer org.freedesktop.Platform.GStreamer.gstreamer-vaapi
```

Опционально требуется:

> Плагин [obs-vkcapture](http://localhost:1313/posts/obs-on-archlinux/#obs-vkcapture)

В настройках obs вкладке "Вывод" будет доступен кодировщик GStreamer Encoder H.264 (выбираете его) и GStreamer Encoder H.265

В Encoder Type выбираем VA-API (не x264 т.к он отвечает за вычисления записи на процессоре)

![image](/images/obs-on-archlinux/1660824506.png)

На этом всё, пробуйте и экспериментируйте, вот какой битрейт необходимо выставлять

Запись:

- 4K (2160p) 60FPS = 65000-60000 bitrate
- 2K (1440p) 60FPS = 45000-50000 bitrate
- Full HD (1080p) 60FPS = 30000 bitrate

Трансляция Twitch:

- Full HD (1080p) = 6000 bitrate

Могу порекомендовать вам в тул-баре obs "Док-панели" выбрать "Статистика" она вам поможет для мониторинга cpu, пропусков кадров, fps и т.д.

### Советы и рекомендации

- [Мелкий совет от Reddit пользователя](https://www.reddit.com/r/linux_gaming/comments/w4i3qf/comment/ih5unkw/?utm_source=share&utm_medium=web2x&context=3)
  > "_...Быстрый профессиональный совет по использованию плагина GStreamer. Введите `cabac=true` в большое поле для дополнительных опций, которое должно дать вам хорошее улучшение соотношения качества/битрейта._"

> В: Что делает опция cabac?
>
> О: Включает режим энтропийного кодирования cabac

- [Source](https://gstreamer.freedesktop.org/documentation/vaapi/vaapih264enc.html?gi-language=c#vaapih264enc:cabac)
- [Wikipedia о cabac](https://ru.wikipedia.org/wiki/CABAC)
