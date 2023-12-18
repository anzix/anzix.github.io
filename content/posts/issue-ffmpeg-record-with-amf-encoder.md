+++
title = "Проблема с записью FFmpeg в связке с AMF кодировщиком"
date = 2023-10-08T23:22:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux"]
+++

При вызове команды

```sh
ffmpeg -vaapi_device /dev/dri/renderD128 -f x11grab -video_size 1920x1080 -framerate 60 -i :0 -c:v h264_amf output.mp4
```

Выхлоп команды такой

```txt
zsh: segmentation fault (core dumped)  ffmpeg -vaapi_device /dev/dri/renderD128 -f x11grab -video_size 1920x1080  60
```

Решением было экспортировать переменную проприетарных драйверов AMDGPU-PRO командой vk_pro (из AUR пакета [amd-vulkan-prefixes](https://aur.archlinux.org/packages/amd-vulkan-prefixes/))

```sh
vk_pro ffmpeg -vaapi_device /dev/dri/renderD128 -f x11grab -video_size 1920x1080 -framerate 60 -i :0 -c:v h264_amf output.mp4
```

Или просто экспортировать переменные напрямую из проприетарных дров AMDGPU-PRO

```sh
VK_ICD_FILENAMES=/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd64.json:/opt/amdgpu-pro/etc/vulkan/icd.d/amd_icd32.json ffmpeg -vaapi_device /dev/dri/renderD128 -f x11grab -video_size 1920x1080 -framerate 60 -i :0 -c:v h264_amf output.mp4
```
