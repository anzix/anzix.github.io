+++
title = "Самый простой тест микрофона"
date = 2023-03-08T19:25:28+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- [Источник_1](https://linuxconfig.org/how-to-test-microphone-with-audio-linux-sound-architecture-alsa)

Необходимо скачать данный пакет для доступа к `arecord` и `aplay`

```sh
sudo pacman -S alsa-utils
```

Открываем терминал и вводим

Показать список звуковых карт и цифровых аудиоустройств

```sh
arecord -l
```

```txt
**** List of CAPTURE Hardware Devices ****
card 0: Webcam [FULL HD 1080P Webcam], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: PCH [HDA Intel PCH], device 0: ALC662 rev3 Analog [ALC662 rev3 Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: PCH [HDA Intel PCH], device 2: ALC662 rev3 Alt Analog [ALC662 rev3 Alt Analog]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 3: Mic [Samson C01U Pro Mic], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

Для тестирования я буду использовать card номер 3 т.е микрофон Samson C01U Pro

```txt
...
card 3: Mic [Samson C01U Pro Mic], device 0: USB Audio [USB Audio]
...
```

Пояснение по опциям

- `-D` - устройство
- `-d` - прерывание записи после # сек (0 - бесконечная запись чтобы остановить ctrl+c).

Пример:

```sh
hw:[номер цифрового устройства из списка arecord -l указанный в card "цифра"],0 выводВфайл.wav
```

Вписываем такую последовательность и жмём enter. Начнётся запись микрофона

```sh
arecord -f S16_LE -d 10 -D "hw:3,0" test-mic.wav
```

Воспроизвести запись

```sh
aplay test-mic.wav
```
