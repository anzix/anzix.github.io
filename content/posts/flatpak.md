+++
title = "!Flatpak"
date = 2023-03-06T21:25:07+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

* [Магазин Flathub](https://flathub.org/home)
* [5 лучших способов управления flatpak](https://www.youtube.com/watch?v=6EWK5qLCv5c)

Установка
```bash
pacman -S flatpak flatpak-xdg-utils flatpak-builder elfutils patch --needed
```

Добавляем репозиторий Flathub и Flathub-Beta
```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo &&
flatpak remote-add --if-not-exists flathub-beta https://flathub.org/beta-repo/flathub-beta.flatpakrepo
```

Обновление приложений или среду выполнения
```bash
flatpak update
```

Разрешить доступ только для чтения к конфигурациям GTK
```bash
sudo flatpak override --filesystem=xdg-config/gtk-3.0:ro
sudo flatpak override --filesystem=xdg-config/gtk-4.0:ro
```

Установка программ с Flathub
```bash
PKGS=(
        'com.discordapp.Discord' # Чат для геймеров
        'com.github.tchx84.Flatseal' # Управление разрешениями Flatpak, для KDE не нужен
        'net.davidotek.pupgui2' # ProtonUpQt
        'com.usebottles.bottles' # Бутылка Wine Менеджер
        'org.blender.Blender'
)
flatpak install -y --noninteractive flathub "${PKGS[@]}"
```

Правила и настройки
```bash
# Использовать wayland
flatpak override --socket=wayland org.blender.Blender
flatpak override --socket=wayland com.discordapp.Discord
# Дать возможность Bottles создавать ярлыки
flatpak override --filesystem=xdg-data/applications com.usebottles.bottles
# Правим discord ярлык для совместимости на Wayland
cp /var/lib/flatpak/app/com.discordapp.Discord/current/active/files/share/applications/com.discordapp.Discord.desktop $HOME/.local/share/applications
sed -i "s|Exec=discord|Exec=flatpak run com.discordapp.Discord --enable-features=UseOzonePlatform,WaylandWindowDecorations --ozone-platform=wayland|g" $HOME/.local/share/applications/com.discordapp.Discord.desktop
```

Установка программ с Flathub-beta
```bash
PKGS=(
        'org.gimp.GIMP' # Редактор изображений (Имеет нативную поддержку wayland)
)
flatpak install -y --noninteractive flathub-beta "${PKGS[@]}"
```

## Удобства / полезное

Для WM (оконных менеджеров) использующих rofi dmenu, и также для тех кто не хочет постоянно перезапускать сессию для появления ярлыков. Создаёт символическую ссылку чтобы ярлыки Flatpak'ов были в /usr/share/applications, и показывались в меню пуск
```bash
ln -s /var/lib/flatpak /usr/share/applications
```

Alias для удобного запуска программ через flatpak, прописываем в `.bashrc` или `.zshrc`
```
alias flat="flatpak run"
```
```
flat [название программы]
```

[Взято из поста Reddit](https://www.reddit.com/r/linux/comments/u3wcm7/easy_flatpak_apps_backupinstallation/)

Экспорт пакетов Flatpak
```bash
flatpak list --columns=application --app > flatpaks.txt
```

Импорт пакетов Flatpak
```bash
xargs flatpak install -y < flatpaks.txt
```

[11.06.2023] Во flatpak скоро будет своя собственная реализация синхронизации пакетов т.е импорт и экспорт называется [flatsync](https://gitlab.gnome.org/Cogitri/flatsync). [Новость в The Linux Experiment](https://youtu.be/n3gMicC8gU0?t=276)


## Полезные опции

* ``-y (--assumeyes)`` - Избавится от вопросов
* ``--noninteractive`` - Без лишних подробностей

