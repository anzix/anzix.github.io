+++
title = "!Стриминг и запись AMF аппаратным декодированием на Arch Linux"
date = 2023-12-11T02:10:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Источники:

- [Arch Wiki FFmpeg AMF](https://wiki.archlinux.org/title/FFmpeg#AMD_AMF)
- [Видео гайд на Arch](https://www.youtube.com/watch?v=SRGAA7PyAEI)
- [Видео гайд на Gentoo](https://www.youtube.com/watch?v=-HWt-yZcGcY)
- [Видео гайд на Fedora (Wayland)](https://www.youtube.com/watch?v=gQtO3tlMqE4)
- [Инструкция на Reddit](https://www.reddit.com/r/linux_gaming/comments/qwqxwd/how_to_enable_amd_amf_encoding_in_obs/)

Инструкция посвящается для тех кто сидит на старых RDNA 1 Polaris 10 т.е RX 470/480/570/580

Кодировщик FFMPEG VAAPI в obs плох и не юзабелен, чаще всего вызывает постоянные тормоза и частый перегруз encoder'а, обычно такое не происходит на новых RDNA 2 видюхах, но вот для RDNA 1 это постоянная проблема.\
В этом поможет проприетарные драйвера Vulkan AMDGPU-PRO и AMF AMDGPU-PRO которые позволят нам запустить obs с данными драйверами и использовать AMD AMF H.264/AVC (через FFmpeg) кодировщик который может существенно повысить качество стрима и записи при минимальном затрате производительности и без перегрузов кодировщика.\
И ещё AMF НЕ работает с open-source'ным amdgpu драйвером.

> Пояснение: AMF [Advanced Media Framework](https://github.com/GPUOpen-LibrariesAndSDKs/AMF) это AMD-шный проприетарный конкурент-альтернатива кодировщика NVENC от Nvidia

Некоторые дистрибутивы не поставляют версию FFMPEG скомпилированной поддержкой AMF на дистрибутивах на основе Arch он уже включён по умолчанию, поэтому вам следует проверить, имеет ли ваша версия FFMPEG поддержку AMF, запустив данную команду

```sh
ffmpeg -encoders | grep -i amf
```

Если вывод этой команды такой...

```txt
 V....D h264_amf             AMD AMF H.264 Encoder (codec h264)
 V....D hevc_amf             AMD AMF HEVC encoder (codec hevc)
```

Тогда вы готовы к дальнейшей установке

Качаем пакеты проприетарных драйверов

> Пояснение: Чтобы у нас появился в obs кодировщик AMD AMF H.264/AVC (via FFmpeg) необходим плагин [obs-streamfx](https://github.com/Xaymar/obs-StreamFX)

```sh
yay -S lib32-vulkan-amdgpu-pro vulkan-amdgpu-pro amf-amdgpu-pro obs-streamfx
```

Для удобства рекомендуется установить AUR пакет [amd-vulkan-prefixes](https://aur.archlinux.org/packages/amd-vulkan-prefixes) позволяющий легко запускать игры, приложения и obs с определённым icd драйвером, не конфликтуя с основным драйвером который к примеру описан о [смене с vulkan на radv (vulkan-radeon)](TODO) таким образом можно выжать максимум производительности из игры и записи (или трансляции) из obs.

```sh
yay -S amd-vulkan-prefixes
```

В терминале вводим переменную `vk_pro` таким образом вызывая проприетарный драйвер (у которого есть кодировщик AMF) запустив вместе с ним obs

```sh
$ vk_pro obs
```

Для тех кто хочет всё запустить вручную необходимо ввести за obs переменную `VK_ICD_FILENAMES="путь до ICD файла amdgpu-pro" obs`

```sh
VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/amd_pro_icd64.json:/usr/share/vulkan/icd.d/amd_pro_icd32.json obs
```

В настройках Вывода - вкладке Трансляция и Запись будет доступен данный кодировщик

![image](/images/streaming-recording-using-amf-enc-in-obs/obs-settings.png)

Теперь для удобства лишь необходимо создать псевдоним (alias) в `.bashrc` или `.zshrc` для удобного запуска

```sh
alias obs="VK_ICD_FILENAMES=/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd64.json:/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd32.json OBS_USE_EGL=1 obs"
# или
alias obs="VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/amd_pro_icd64.json:/usr/share/vulkan/icd.d/amd_pro_icd32.json OBS_USE_EGL=1  obs"
```

Не знаю почему, но если я использую **amd_pro_icd64.json** из ``/usr/share/vulkan/icd.d/`` у меня при изменении битрейта и начав запись obs вылетает впадая в segfault

Если у кого-то также я советую установить отдельные части драйверов AMDGPU-PRO Vulkan и AMF которые будут закачаны в ``/opt`` и запускать данные `VK_ICD_FILENAMES` от туда

```sh
git clone https://github.com/DoomPenguin9/amdgpu-pro-vulkan-and-amf-only.git
cd amdgpu-pro-vulkan-and-amf-only
makepkg -si
```
