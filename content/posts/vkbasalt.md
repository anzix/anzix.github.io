+++
title = "!VkBasald"
date = 2023-03-07T11:15:38+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

* https://github.com/DadSchoorse/vkBasalt
* [Полезная информация о vkBasalt](https://linuxreviews.org/VkBasalt)

Качаем AUR пакеты (для работы на 32 bit'ных играх необходим lib32-vkbasalt)
```
yay -S vkbasalt lib32-vkbasalt reshade-shaders-git
```

Необходимо создать конфиг файл с данным содержанием

nvim ~/.config/vkBasalt/vkBasalt.conf
```sh
#:se ft=sh
reshadeTexturePath = /opt/reshade/textures
reshadeIncludePath = /opt/reshade/shaders

# Увеличение контрастности для старых игр (может привести к большому снижению производительности)
FakeHDR = /opt/reshade/shaders/FakeHDR.fx

# Cглаживание текстур (альтернатива FXAA для AMD)
SMAA = /opt/reshade/shaders/SMAA.fx

casSharpness=0.7 # Определяет степень резкости шейдера cas
toggleKey = Home # Клавиша активации vkBasalt
effects = SMAA:cas
```

Вот список интересных фильтров:
* FilmGrain2 - эффект киноплёнки
* Curves - увеличение контрастности для новых игр
* FakeHDR - увеличение контрастности для старых игр (может привести к большому снижению производительности)
* SMAA - сглаживание текстур (альтернатива FXAA для AMD)

AMD Fidelity FX Sharpness ..% если игра новая тогда ставим 50% если старая тогда ставим 100%

По стандарту стоит 75%

