+++
title = "Плагин подавления шума микрофона в реальном времени noise-suppression-for-voice"
date = 2023-03-07
draft = false
[taxonomies]
categories = []
tags = ["linux", "obs"]
+++

- [Github страница проекта](https://github.com/werman/noise-suppression-for-voice)
- [Видео Гайд для Windows](https://www.youtube.com/watch?v=lrrUXVoNpqg)

В моём примере всё будет делаться на pipewire

## Требование

Ваш микрофон должен иметь поддержку частоту дискретизации в 48000 Hz. Pipewire
по умолчанию переводит частоту дискретизации на 48000. Однако на всякий
проверить это можно данной командой

```bash
cat /proc/asound/card3/stream0
```

<pre>
Samson Technologies Samson C01U Pro Mic at usb-0000:00:1d.0-1.6, full speed : USB Audio

Playback:
 Status: Running
   Interface = 1
   Altset = 1
   Packet Size = 196
   Momentary freq = <u><b>48000 Hz</b></u> (0x30.0000)
 Interface 1
   Altset 1
   Format: S16_LE
   Channels: 2
   Endpoint: 0x01 (1 OUT) (SYNC)
   Rates: 44100, 48000
   Bits: 16
   Channel map: FL FR
</pre>

Как видно из `Momentary freq` мне ничего менять не нужно

## Установка и настройка

Качаем плагин

```bash
sudo pacman -S noise-suppression-for-voice
```

Создаём необходимую папку

```bash
mkdir -p ~/.config/pipewire/pipewire.conf.d/
```

Вставляем [это всё](https://github.com/werman/noise-suppression-for-voice/blob/9c4e5c28d8950e2cef837d8a0abd36c2fd9b5c2d/README.md?plain=1#L72-L104)
но меняем на то что показано ниже

```bash
nvim ~/.config/pipewire/pipewire.conf.d/99-input-denoising.conf
```

```bash
...........
            .........
            # Название
            node.description = "Noise Canceling source" -->> "Шумодав микро"
            media.name = "Noise Canceling source" -->> "Шумодав микро"
            ........
                    ........
                    # Указываем путь библиотеки из скачанного пакета noise-suppression-for-voice
                    plugin = /path/to/librnnoise_ladspa.so -->> plugin = /usr/lib/ladspa/librnnoise_ladspa.so
                    ......
        ..........

.........
```

Перезагружаем pipewire

```bash
systemctl --user restart pipewire
```

## Настройка в OBS

Открываем obs, и в Mic/Aur жмём на шестерёнку и выбираем "Свойства". В выпадающем
списке выбираем "Шумодав микро"

![image](/images/noise-suppression-for-voice-archlinux/obs.png)

![image](/images/noise-suppression-for-voice-archlinux/obs-noise-suppression-source.png)

Теперь у вас активированный шумодав на вашем микрофоне
