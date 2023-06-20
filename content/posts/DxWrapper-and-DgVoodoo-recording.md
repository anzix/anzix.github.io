+++
title = "Запись старых игр (DgVoodoo или DxWrapper+DXVK) с использованием кодировщика GStreamer Vaapi и захватом obs-vkcapture"
date = 2023-03-04T17:04:39+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++

Таким образом можно снимать видео с obs без проблем, как это всё сделать описано ранее

> **ВНИМАНИЕ!** Не ставьте [obs-vkcapture](https://aur.archlinux.org/packages/obs-vkcapture) [lib32-obs-vkcapture](https://aur.archlinux.org/packages/lib32-obs-vkcapture) (т.е без git на конце) они являются хоть и стабильными и работают, НО с ними у меня не показывается MangoHud если юзать на старой игре с DgVoodoo или DxWrapper + DXVK

Если вы уже настроили GStreamer Vaapi в Obs и скачали правильные версии пакетов [obs-vkcapture-git](https://aur.archlinux.org/packages/obs-vkcapture-git) [lib32-obs-vkcapture-git](https://aur.archlinux.org/packages/lib32-obs-vkcapture-git).
Достаточно просто прописать в начале при запуске игры переменную: ``obs-gamecapture``

**Работает**: Захват игры + отображение Mangohud

**Проверено на**:
* [TMNT2: Battle Nexus](https://www.pcgamingwiki.com/wiki/Teenage_Mutant_Ninja_Turtles_2:_Battle_Nexus)
* [Freedom Fighters](https://www.pcgamingwiki.com/wiki/Freedom_Fighters)
* [Shrek 2: The Game](https://www.pcgamingwiki.com/wiki/Shrek_2:_The_Game)
* [Tomb Raider: Anniversary](https://www.pcgamingwiki.com/wiki/Tomb_Raider:_Anniversary)
* [HP1](https://www.pcgamingwiki.com/wiki/Harry_Potter_and_the_Philosopher%27s_Stone)
* [HP2](https://www.pcgamingwiki.com/wiki/Harry_Potter_and_the_Chamber_of_Secrets)
* [NFS Carbon](https://www.pcgamingwiki.com/wiki/Need_for_Speed:_Carbon)
* [BloodRayne](https://www.pcgamingwiki.com/wiki/BloodRayne)
