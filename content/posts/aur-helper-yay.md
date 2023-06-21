+++
title = "Настройка yay, разное полезное"
date = 2023-04-15T20:54:54+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Установка yay
```sh
git clone https://aur.archlinux.org/yay-bin.git
cd yay-bin && makepkg -si --noconfirm
cd ~ && rm -rf yay-bin
```

Пояснение:
* `--nodiffmenu` - Не спрашивать об показе изменений (diff)
* `--nocleanmenu` - Не спрашивать о пакетах для которых требуется очистить кэш сборки
* `--removemake` - Всегда удалять зависимости для сборки (make) после установки
* `--batchinstall` - Ставит каждый собранный пакеты в очередь для установки (легче мониторить что происходит)

Просто вставляем в терминал и yay настроен к работе
```sh
yay --save --nodiffmenu --nocleanmenu --removemake --batchinstall
```

## !Полезные опции yay

Позволяет отредактировать PKGBUILD пакета перед установкой
```sh
yay -S --editmenu [AUR пакет]
```

Создать базу данных dev пакетов *-git, которые были установлены без yay. Эта команда должна быть запущена только один раз.

```sh
yay -Y --gendb
```

Позволяет искать обновления *-git пакетов до последнего коммита
```sh
yay --devel
```


## Полезное для pacman и yay

Обновление всей системы (самого ядра, окружения рабочего стола, программ из pacman и из AUR) производится одной командой:
```sh
yay -Syyu
```

Использовать в первую очередь если очень долго не обновлялись
```sh
sudo pacman -Sy archlinux-keyring && sudo pacman -Syyu
```
## TUI магазины приложений с помощью fzf
```sh
sudo pacman -S fzf
```

Pacstore
```sh
pacman -Slq | fzf -m --preview 'cat <(pacman -Si {1}) <(pacman -Fl {1} | awk \"{print \$2}\")' | xargs -ro sudo pacman -S
```
Aurstore
```sh
sudo pacman -F | yay -Slq | fzf -m --preview 'cat <(yay -Si {1}) <(yay -Fl {1} | awk \"{print \$2}\")' | xargs -ro yay -S
```
## Экспорт & импорт пакетов pacman и AUR
* [Источник](https://wiki.archlinux.org/title/Pacman/Tips_and_tricks#Install_packages_from_a_list)

Экспорт пакетов pacman
```sh
pacman -Qqen > ~/dotfiles/pacman-pkglist.txt
```
Экспорт пакетов aur
```sh
pacman -Qqem > ~/dotfiles/AUR-pkglist.txt
```
Экспорт pacman+aur
```sh
pacman -Qqe > ~/dotfiles/pkglist.txt
```

Импорт пакетов pacman
```sh
sudo pacman -S --needed - < pacman-pkglist.txt
```

Импорт пакетов AUR используя yay
```sh
yay -S --needed - < AUR-pkglist.txt
```

Импорт всех пакетов
```sh
yay -S --needed - < pkglist.txt
```

