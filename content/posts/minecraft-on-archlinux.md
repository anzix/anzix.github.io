+++
title = "Minecraft на Arch Linux"
date = 2023-03-16T11:28:14+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++

## Оффлайн пиратки

Список доступных:
1. [TL Legacy](https://tlaun.ch/)/[VK](https://vk.link/tl_mc_launcher)/[AUR](https://aur.archlinux.org/packages/tlauncher/): `yay -S tlauncher`
2. [PollyMC](https://github.com/fn2006/PollyMC) - форк PolyMC с оффлайн аккаунтом/[AUR](https://aur.archlinux.org/packages/pollymc-bin): `yay -S pollymc-bin`

### Ручной запуск jar лаунчера майна

Ручной запуск jar лаунчера майна

Качаем необходимы пакеты
```sh
sudo pacman -S jre-openjdk jre-openjdk-headless java-runtime-common
```
Далее переходим (через терминал) в папку на скачанный jar файл майна и вводим для запуска
```sh
java -jar [имяфайла].jar
```

## Лицензионные аккаунты

Некоторые из них обладают возможностью скачиванию и обновлением модов прямо в клиенте

1. [Prism Launcher](https://prismlauncher.org/)/[AUR](https://aur.archlinux.org/packages/prismlauncher-bin): `yay -S prismlauncher-bin`
2. [PolyMC](https://polymc.org/)/[AUR](https://aur.archlinux.org/packages/polymc-bin): `yay -S polymc-bin`
3. [MultiMC](https://github.com/MultiMC/Launcher)/[AUR](https://aur.archlinux.org/packages/multimc-bin): `yay -S multimc-bin`
4. [ATLauncher](https://atlauncher.com/)/[AUR](https://aur.archlinux.org/packages/atlauncher-bin): `yay -S atlauncher-bin`
5. [GDLauncher](https://gdevs.io/)/[AUR](https://aur.archlinux.org/packages/gdlauncher-bin): `yay -S gdlauncher-bin`
