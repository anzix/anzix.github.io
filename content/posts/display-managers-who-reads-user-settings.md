+++
title = "Дисплей менеджеры которые читают пользовательские настройки"
date = 2023-10-20T14:50:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Обновление: 30.11.23

- [Пользовательский конфиг файл для переменных](https://www.freedesktop.org/software/systemd/man/environment.d.html)

Wayland и Xorg доп-но экспортирует пользовательские переменные в ``~/.config/environment.d/*.conf`` независимо от $SHELL или дисплей менеджеров

Пример:

```sh
mkdir ~/.config/environment.d
nvim ~/.config/environment.d/envvars.conf
```

## !Xorg X11

- [Arch WIki Environment variables - Per user](https://wiki.archlinux.org/title/Environment_variables#Per_user)

**Ручной запуск из TTY** - Подхватывает настройки от выбранного **$SHELL**

TTY не читает ``~/.profile`` если выбрана оболочка zsh а не bash

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```

xinitrc подхватывается переменной **$XINITRC** прописанной в ``~/.zprofile`` и может находится где угодно

**LightDM** - подхватывает настройки независимо от **$SHELL**, но необходимо наличие ``~/.xprofile``

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.profile
ln -svi /home/$USER/dotfiles/base/x/.config/X11/xinitrc ~/.xprofile
```

**SDDM** - Подхватывает настройки от выбранного **$SHELL**, но необходимо наличие ``~/.xprofile``
sddm - не читает ``~/.profile`` если выбрана оболочка zsh а не bash

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
ln -svi /home/$USER/dotfiles/base/x/.config/X11/xinitrc ~/.xprofile
```

**LXDM** - подхватывает настройки независимо от **$SHELL** как LightDM, но необходимо наличие ``~/.xprofile``

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.profile
ln -svi /home/$USER/dotfiles/base/x/.config/X11/xinitrc ~/.xprofile
```

**GDM** - Подхватывает настройки от выбранного **$SHELL**

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.profile

# Только при запуске Xorg сессии выполняется ~/.xprofile при его наличии
ln -svi /home/$USER/dotfiles/base/x/.config/X11/xinitrc ~/.xprofile
```

**[Ly](https://github.com/fairyglade/ly)** - Глючное шо капец

Подхватывает настройки от выбранного **$SHELL**

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```

Запуск Xinitrc можно указать где угодно (не проверено)

```sh
nvim /etc/ly/config.ini
```

```txt
...
# xinitrc
#xinitrc = ~/.xinitrc
...
```

```sh
sudo sed -i 's/^#xinitrc.*/xinitrc = $XINITRC/' /etc/ly/config.ini  # Имеет графические проблемы
sudo sed -i 's/^#lang.*/lang = ru/' /etc/ly/config.ini
```

## !Wayland

- [Arch Wiki Environment variable - Wayland](https://wiki.archlinux.org/title/Environment_variables#Per_Wayland_session)

**Ручной запуск из TTY** - Подхватывает настройки от выбранного **$SHELL**

TTY не читает ``~/.bash_profile`` если выбрана оболочка zsh

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```

**LightDM** - Подхватывает настройки от выбранного **$SHELL**

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```

**GDM** - Подхватывает настройки от выбранного **$SHELL** как LightDM

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```

**SDDM** - Подхватывает настройки от выбранного **$SHELL**

sddm - не читает ``~/.profile`` если выбрана оболочка zsh а не bash

```sh
ln -svi /home/$USER/dotfiles/base/zsh/.config/zsh/profile.zsh ~/.zprofile
```
