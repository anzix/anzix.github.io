+++
title = "Менеджмент сессии аудиоустройств - Wireplumber"
date = 2023-03-06
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- [Видео инструкция с объяснениями](https://www.youtube.com/watch?v=Zv1P6-kUn0c)

Скачиваем данный пакет, pipewire-media-session заменится на wireplumber.

```bash
sudo pacman -S wireplumber
```

После чего перезапускаем ПК

## Изменить названия аудиоустройств

В терминале вводим

```bash
pw-cli list-objects Node
```

В данном примере возьму выход на наушники через jack микрофона Samson C01U Pro.
Находим длинное имя для выхода (output) устройства и копируем его

<pre>
........
id 32, type PipeWire:Interface:Node/3
      object.serial = "58"
      object.path = "alsa:pcm:3:front:3:playback"
      factory.id = "18"
      client.id = "35"
      device.id = "48"
      priority.session = "1009"
      priority.driver = "1009"
      node.description = "C01U Pro condenser microphone Аналоговый стерео"
      node.name = "<b>alsa_output.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.analog-stereo</b>"
      node.nick = "Samson C01U Pro Mic"
      media.class = "Audio/Sink"
</pre>

То есть я копирую **`alsa_output.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.analog-stereo`**

И тут же ищем и копируем для входного (input) устройство

<pre>
........
id 52, type PipeWire:Interface:Node/3
      object.serial = "58"
      object.path = "alsa:pcm:3:hw:3:capture"
      factory.id = "18"
      client.id = "35"
      device.id = "48"
      priority.session = "2000"
      priority.driver = "2000"
      node.description = "C01U Pro condenser microphone Моно"
      node.name = "<b>alsa_input.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.mono-fallback</b>"
      node.nick = "Samson C01U Pro Mic"
      media.class = "Audio/Source"
</pre>

Здесь также, но я копирую **`alsa_input.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.mono-fallback`**

Создаём структуру папки

```bash
mkdir -p ~/.config/wireplumber/wireplumber.conf.d/
```

И создаём внутри него файл `~/.config/wireplumber/main.lua.d/51-Samson_C01UPro-rename.conf`
внутри которого будет объявлено входное и выходное аудиоустройство в более понятном
виде.

> Пояснение:
>
> В **`node.description`** указываем ваше укороченное имя, это название будет
> применятся на общих для аудиомикшерах программ таких как pavucontrol, pasystray,
> cli утилита `pulsemixer` и т.д
>
> А **`node.nick`** указываем то же самое но название но оно уже предназначено
> для применения наименования для профессиональных программ таких как patchbay,
> qpwgraph, QjackCtl и т.д

```json
monitor.alsa.rules = [
   {
      matches = [
         {
            node.name = "alsa_output.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.analog-stereo"
         }
      ]
      actions = {
         update-props = {
            node.description = "C01U Pro",
            node.nick = "C01U Pro",
         }
      }
   }
   {
      matches = [
         {
            node.name = "alsa_input.usb-Samson_Technologies_Samson_C01U_Pro_Mic-00.mono-fallback"
         }
      ]
      actions = {
         update-props = {
            node.description = "C01U Pro",
            node.nick = "C01U Pro",
         }
      }
   }
]
```

Сохраняем и перезагружаем pipewire или ребутимся

```bash
systemctl --user restart pipewire
```

После перезапуска в pavucontrol и Cli утилита `pulsemixer` будет показывать ваше
укороченное название аудиоустройство которое вы указали

Для вывода из колонок (Speakers) находите тоже в node

```bash
pw-cli list-objects Node
```

<pre>
      ......
      node.description = "Встроенное аудио Аналоговый стерео"
      node.name = "<b>alsa_output.pci-0000_00_1b.0.analog-stereo</b>"
      node.nick = "ALC662 rev3 Analog"
      media.class = "Audio/Sink"
</pre>

Копирую **`alsa_output.pci-0000_00_1b.0.analog-stereo`**

Создаём файл `~/.config/wireplumber/wireplumber.conf.d/51-Speakers-rename.conf` с содержимым
внутри

> Информация: Мои колонки иногда могут иметь другое название `node`, то есть
> в конфиге записываю 2 узла в качестве применения свойств переименования

```json
monitor.alsa.rules = [
   {
      matches = [
         {
            node.name = "alsa_output.pci-0000_00_1b.0.analog-stereo.2"
         }
      ]
      actions = {
         update-props = {
            node.description = "Колонки",
            node.nick = "Колонки",
         }
      }
   }
   {
      matches = [
         {
            node.name = "alsa_output.pci-0000_00_1b.0.analog-stereo"
         }
      ]
      actions = {
         update-props = {
            node.description = "Колонки",
            node.nick = "Колонки",
         }
      }

   }
]
```

При перезагрузке pipewire вот что у меня получилось в pulsemixer
![image](/images/wireplumber-audio-session-management/pulsemixer-rename-audio.png)

## Отключение ненужного выхода аудио HDMI GPU

В этот раз ищем не в узлах (Node) а в Device

```bash
pw-cli list-objects Device
```

<pre>
.............
id 42, type PipeWire:Interface:Device/3
      object.serial = "42"
      factory.id = "14"
      client.id = "32"
      device.api = "alsa"
      device.description = "Ellesmere HDMI Audio [Radeon RX 470/480 / 570/580/590]"
      device.name = "<b>alsa_card.pci-0000_03_00.1</b>"
      device.nick = "HDA ATI HDMI"
      media.class = "Audio/Device"
..............
</pre>

То есть копируем **`alsa_card.pci-0000_03_00.1`**

Создаём файл `~/.config/wireplumber/wireplumber.conf.d/51-amd-disable.conf` с
содержимым внутри

> Пояснение:
>
> Под `matches` вместо **`node.name`** должно быть **`device.name`**
>
> Под **`apply_properties`** вводите **`["device.disabled"] = true`** это применяет
> отключение данного аудиоустройства которое вы выставили в matches

```json
monitor.alsa.rules = [
   {
      matches = [
         {
            device.name = "alsa_card.pci-0000_03_00.1"
         },
         {
            device.name = "alsa_output.pci-0000_03_00.1.hdmi-stereo-extra5"
         }
      ]
      actions = {
         update-props = {
            device.disabled = true
         }
      }
   }
]
```

После перезагрузки pipewire в pavucontrol больше не будет появлятся ненужное
аудио устройство
![image](/images/wireplumber-audio-session-management/pavucontrol-hdmi-gpu-disable.png)
![image](/images/wireplumber-audio-session-management/hdmi-audio-gpu-disable-pulsemixer.png)
