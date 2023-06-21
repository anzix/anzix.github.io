+++
title = "Решение проблем с композитором Picom"
date = 2023-04-15T18:33:12+05:00
draft = false
[taxonomies]
categories = ["solving-problem"]
tags = ["linux"]
+++

## 1.(Решил) Проблема

Столкнулся с графическим глюком при переходе в полноэкранный (win+f) на любом терминале, но когда двигаешь мышкой или нажав клавишу - пропадает

![](/images/solving-problem-compositor-picom/blur-bug.jpg)

<font color="green">Решается</font> это отключением fading (затухание)

Либо отключить dual_kawase

## 2.(Решил) Проблема

При вызове maim (минимального скриншотера) появляется размытый экран вместо чистого

<font color="green">Решается</font> это добавлением в исключение blur'а picom

maim вызывает slop поэтому необходимо именно его добавить в исключение
```
blur-background-exclude = [
   "class_g = 'slop'",
];
```

## 3.(Решил) Проблема

Некоторые элементы (например toolbar, всплыв-е окна и т.д) имеют на краях слой прозрачности от blur'а dual_kawase

<font color="green">Решается</font> это добавлением данной строки

```
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

<font color="green">Решил</font> это добавлением в exclude (исключение) игры или программы используя xprop
```
unredir-if-possible-exclude = [
        "class_g = 'looking-glass-client' && !focused",
        "class_g = 'Serious Sam Fusion 2017 - Linux - 64bit'",
        "class_g != 'Gimp'",
        "class_g != 'i3lock'"
];
```

