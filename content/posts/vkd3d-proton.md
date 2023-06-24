+++
title = "!vkd3d-proton (ретранслятор DirectD3D 12 в Vulkan от Valve)"
date = 2023-03-04T22:26:56+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Ретранслятор от офф реализации wine

```bash
sudo pacman -S vkd3d lib32-vkd3d
```

Самый простой и незатратный способ проверить vkd3d-proton (форк vkd3d от Valve) - это установить демо Resident Evil 3 в Steam (7.2GB)

Для проверки используя стоковый (или кастомный) Wine есть демо на Archive
[Resident Evil 3 Remake: Raccoon City Demo - PC](https://archive.org/details/resident-evil-3-remake-raccoon-city-demo-pc/)

Накатив vkd3d-proton на префикс можно также двумя методами как и с DXVK

Через winetricks

```bash
WINEPREFIX="путь/до/префикса" wineprefix -q vkd3d
```

Используя [AUR пакет vkd3d-proton-bin](https://aur.archlinux.org/packages/vkd3d-proton-bin)

```bash
WINEPREFIX="путь/до/префикса" setup_vkd3d_proton install
```

**Неактуально для wine 8.1 и более**: выставяем режим совместимости Windows 10 используя winecfg
