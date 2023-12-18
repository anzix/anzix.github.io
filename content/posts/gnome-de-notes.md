+++
title = "Окружение GNOME, заметки и прочее"
date = 2023-10-26T01:20:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

## !Последовательный интерфейс тулкита QT в GNOME следуя стилю LibAdwaita

- [KvLibadwaita Github](https://github.com/GabePoel/KvLibadwaita)
- [QAdwaitaDecorations](https://github.com/FedoraQt/QAdwaitaDecorations)
- [Reddit пост](https://www.reddit.com/r/archlinux/comments/16osm1k/adwaitacolorschemes_package_gone_from_the_repos/)

Для глобальной внешности QT 5/6 качаем все эти пакеты

```sh
yay -S qt5ct qt6ct kvantum kvantum-theme-libadwaita-git qadwaitadecorations-qt6
```

Пояснение по пакетам:

- `qt5ct` `qt6ct` - конфигураторы тулкитов qt5/qt6
- `kvantum` - движок для QT 5/6 приложений
- `kvantum-theme-libadwaita-git` - тема для kvantum'а
- `qadwaitadecorations-qt6` `qadwaitadecorations-qt5` - новая декорация окон для QT 5/6 Wayland приложений

Выставляем KvLibadwaitaDark в качестве темы для QT 5/6 приложений

```sh
mkdir -v ~/.config/Kvantum
echo 'theme=KvLibadwaitaDark' > ~/.config/Kvantum/kvantum.kvconfig
```

Открываем оба qt5ct и qt6ct.\
Выбираем kvantum или kvantum-dark в качестве стиля темы.\
Применяем изменения.

И выставляем переменные в ``~/.config/environment.d/*`` или ``/etc/environment``

```conf
# Для последовательного стиля приложений Qt
# Устанавливает декорацию окон Wayland
# QAdwaitaDecorations
QT_WAYLAND_DECORATION=adwaita
# Устанавливает тему платформы по умолчанию
QT_QPA_PLATFORMTHEME=qt6ct

# Курсор
XCURSOR_THEME=Adwaita
XCURSOR_SIZE=24
```

Производим перелогин используя команду `systemctl soft-reboot` и стилизация сделана\
Теперь когда вы хотите поменять оформление тулкита QT вам нужно открывать kvantum и менять его там\
И после применения выставленный движок qt6ct подхватывают стиль из kvantum и прорисовывает его

Проблема: декорация окон немного не правильно отображается, кнопка закрыть не прорисована

![image](/images/gnome-de-notes/Screenshot_20231126_020000_e.png)

Решение: Данная не прорисовка происходит только в qt6 приложениях\
Необходимо открыть `qt6ct` и во вкладке "Стиль значков" выбрать Adwaita\
Проблема решена

![image](/images/gnome-de-notes/Screenshot_20231126_020316.png)

## Экспорт и Импорт настроек dconf

- [Источник](https://askubuntu.com/questions/984205/how-to-save-gnome-settings-in-a-file)

Экспорт настроек

```sh
dconf dump / > dconf_backup.txt
```

Импорт настроек

```sh
dconf load / < dconf_backup.txt
```

Просмотр настроек рекурсивно с поиском опции

```sh
gsettings list-recursively | grep [опция]
```

## Горячие клавиши GNOME

1. Смена вирт. столов: Ctrl+Alt + стрелки вправо влево
2. Свернуть окно: Super+h
3. Переместить окно в другое раб. стол: Super+Shift+PageDown/PageUp
4. Resize окна: Super+MMC (средняя кнопка мыши)

### Горячие клавиши в GNOME файлы

1. Навигация по родительским директориям: Alt+стрелки вверх вниз

## Gnome различные расширения

- [systemd-manager](https://github.com/hardpixel/systemd-manager)
- [openweather](https://gitlab.com/skrewball/openweather)
- [middleclickclose](https://github.com/p91paul/middleclickclose)
- [Угол показа обзора вниз](https://github.com/jdoda/hotedge): [AUR](https://aur.archlinux.org/packages/gnome-shell-extension-hot-edge-git)
- [gnome-shell-extension-disable-unredirect](https://github.com/kazysmaster/gnome-shell-extension-disable-unredirect) - без него во многих играх в полноэкранном режиме не показывается курсор

## GTK Пакеты для GNOME (Flatpak)

- [Mousai](https://flathub.org/apps/io.github.seadve.Mousai) - Распознавание музыки
- [Boatswain](https://flathub.org/apps/com.feaneron.Boatswain) - Elgato Stream Decks контроль
- [SaveDesktop](https://flathub.org/apps/io.github.vikdevelop.SaveDesktop) - Сохранение конфигов DE

## Различное полезное

1. Имена (id) расширений

ID GNOME расширений находятся по пути ``/usr/share/gnome-shell/extensions/``\
Некоторые похоже с версии Gnome 45 ID расширений могут находится по пути `~/.local/share/gnome-shell/extensions/`
