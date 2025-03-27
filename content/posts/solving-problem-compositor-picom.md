+++
title = "Решение проблем с композитором Picom"
date = 2023-04-15
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux"]
+++

## 1.(Решил) Проблема

Столкнулся с графическим глюком при переходе в полноэкранный (win+f) на любом
терминале, но когда двигаешь мышкой или нажав клавишу - пропадает

![image](/images/solving-problem-compositor-picom/blur-bug.jpg)

Решается это отключением fading (затухание)

Либо отключить dual_kawase

## 2.(Решил) Проблема

При вызове maim (минимального скриншотера) появляется размытый экран вместо чистого

Решается это добавлением в исключение blur'а picom

maim вызывает slop поэтому необходимо именно его добавить в исключение

```txt
blur-background-exclude = [
   "class_g = 'slop'",
];
```

## 3.(Решил) Проблема

Некоторые элементы (например toolbar, всплыв-е окна и т.д) имеют на краях слой
прозрачности от blur'а dual_kawase

Решается это добавлением данной строки

```txt
wintypes: {
    unknown = { blur-background = false; };
    desktop = { blur-background = false; };
    dock = { blur-background = false; shadow = false; };
    toolbar = { blur-background = false; };
    menu = { blur-background = false; };
    utility = { blur-background = false; };
    splash = { blur-background = false; };
    dialog = { blur-background = false; };
    normal = { blur-background = true; shadow = false; };
    dropdown_menu = { blur-background = false; };
    popup_menu = { blur-background = false; };
    tooltip = { blur-background = false; };
    notify = { blur-background = true; shadow = false; };
    combo = { blur-background = false; };
    dnd = { blur-background = false; };
};
```

## !4.(???) Проблема

После применения [оптимизации композитора picom для полноэкранного режима]({{< ref "/content/posts/compositor-picom.md#оптимизация-picom-для-полноэкранного-режима-в-играх" >}}) я столкнулся с проблемой вылета игры Serious Sam Fusion 2017 при переключении рабочего пространства

Решил это добавлением в exclude (исключение) игры или программы используя xprop

```txt
unredir-if-possible-exclude = [
        "class_g = 'looking-glass-client' && !focused",
        "class_g = 'Serious Sam Fusion 2017 - Linux - 64bit'",
        "class_g != 'Gimp'",
        "class_g != 'i3lock'"
];
```
