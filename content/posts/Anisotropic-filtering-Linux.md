+++
title = "!Анизотропная фильтрация (Anisotropic filtering) на Linux (RADV Mesa)"
date = 2023-03-04T18:06:02+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++
[Сурс](https://www.pcgamingwiki.com/wiki/Glossary:Anisotropic_filtering_(AF)#Mesa3D)

Для тех кто [использует RADV реализацию Vulkan драйверов от Mesa](/posts/vulkan-radeon-mesa-implementation/) экспортируем данную переменную изменяя необходимое значение (0, 2, 4, 8, 16)
```bash
RADV_TEX_ANISO=16
```

Можно использовать в "Параметрах запуска Steam" или в терминале, используя стандартный Wine

