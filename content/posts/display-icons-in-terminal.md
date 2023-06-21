+++
title = "!Отображение иконок в терминале"
date = 2023-03-29T23:39:35+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
Для отображения иконок необходимо юзать шрифты из семейства Nerd. Их очень много, на любой вкус и цвет, и все они имеют поддержку иконок [exa](https://archlinux.org/packages/community/x86_64/exa/)/[lsd](https://archlinux.org/packages/community/x86_64/lsd/)/[colorls](https://aur.archlinux.org/packages/ruby-colorls) а также [powerlevel10k](https://github.com/romkatv/powerlevel10k) для вашего терминала.

Примеры шрифтов:
1. Hack Nerd Font
```sh
sudo pacman -S ttf-hack-nerd
```
2. JetBrains Mono Nerd Font
```sh
sudo pacman -S nerd-fonts-jetbrains-mono
```
3. SauceCodePro Nerd Font
```sh
sudo pacman -S ttf-sourcecodepro-nerd
```

Для большего выбора пропатченных шрифтов Nerd смотрите [на официальных репозиториях арч](https://archlinux.org/packages/?sort=&q=ttf+nerd&maintainer=&flagged=) или на [AUR](https://aur.archlinux.org/packages?K=nerd-fonts&SB=p)

**Применение шрифта для терминала alacritty**:
```sh
nano ~/.config/alacritty/alacritty.yml
```
Раскомментируем строчки а также вводим наш шрифт как показано ниже
```sh
font:
 normal:
   family: JetBrains Mono Nerd Font
```
> Рекомендация: Вы можете также дополнительно расписать шрифт JetBrains Mono Nerd Font на других начертаниях (bold, italic, bold italic) в вашем конфиг файле.

**Применение шрифта для терминала kitty**:

Создать или открыть `~/.config/kitty/kitty.conf` и добавить в строчку данный шрифт
```sh
font_family JetBrains Mono Nerd Font
```
Шрифт применился, теперь иконки будут нормально отображаться

Если у вас установлен [powerlevel10k](https://github.com/romkatv/powerlevel10k) после применения шрифта необходимо вызвать мастера настройки приглашения p10k данной командой
```sh
p10k configure
```

