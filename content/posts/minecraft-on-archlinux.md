+++
title = "Minecraft на Arch Linux"
date = 2023-03-16
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Здесь будут показаны способы з

## Java лаунчеры

### Оффлайн пиратки

Список доступных:

1. [Legacy Launcher](https://llaun.ch/)

   Сайт [llaun.ch](https://llaun.ch/) нужно заходить **обязательно с RU ip адресса**
   Так, не будет никаких ограничений из-за правообладателя Mojang AB

   - [VK](https://vk.link/tl_mc_launcher)
   - ~~[AUR](https://aur.archlinux.org/packages/legacylauncher/): `yay -S legacylauncher`~~

   Есть два метода запуска:

   1. Используя jar файл

      Legacy Launcher больше не работает на 22 версии Java. Скачав 21 версию и
      выставив её глобально лаунчер запуститься

      ```sh
      # Скачиваем 21 версию Java
      sudo pacman -S jre21-openjdk java-runtime-common

      # Выставляем глобально для системы
      sudo archlinux-java set java-21-openjdk

      # Запускаем
      java -jar LegacyLauncher_legacy.jar
      ```

   2. Flatpak

      Legacy Launcher есть также на [Flathub](https://flathub.org/apps/ch.tlaun.TL).
      Однако там отключена возможность создания оффлайн аккаунта

      Переопределить это поведение можно двумя способами

      1. Использую данную команду:

         ```sh
         flatpak --user override ch.tlaun.TL --env=TL_BOOTSTRAP_OPTIONS="-Dtl.useForce"
         ```

      2. Достать файл `profile.json` который содержит информацию аутентификации
         и заменить его текущим

         TODO: Не проверено

         В Java версии он называется `tlauncher_profile.json` (находится в
         `~/.minecraft/`) и он должен быть в каталоге Flatpak версии
         legacylauncher

         Его ещё можно достать из Windows, файл называется `profile.json`

2. [PollyMC](https://github.com/fn2006/PollyMC)

   Это форк PolyMC с оффлайн аккаунтом

   UPD: больше не развивается

   Есть [AUR](https://aur.archlinux.org/packages/pollymc-bin) пакет который можно
   установить командой

   ```sh
   yay -S pollymc-bin
   ```

3. [Prism Launcher](https://prismlauncher.org/)

   Вот несколько относящихся к оффлайну Prism Launcher'а

   TODO: оба не проверены

   1. [Обход проверки PrismLauncher на наличии настоящего Microsoft MSA аккаунта](https://github.com/antunnitraj/Prism-Launcher-PolyMC-Offline-Bypass)

   2. [PrismLauncher форк с заменой учёток Mojang на Ely.by](https://github.com/Octol1ttle/PrismLauncher-elyby)

4. [Fjord Launcher](https://github.com/unmojang/FjordLauncher)

   Ещё один форк Prism Launcher (замена PollyMC)

5. [Modrinth Offline](https://github.com/Arian8j2/modrinth-offline)

   Форк Modrinth но с оффлайн возможностью логина

### Лицензионные аккаунты

Некоторые из них обладают возможностью скачиванию и обновлением модов прямо в клиенте

1. [Prism Launcher](https://prismlauncher.org/)

   Установка [AUR](https://aur.archlinux.org/packages/prismlauncher-bin) пакета
   пакет

   ```sh
   yay -S prismlauncher-bin
   ```

2. [PolyMC](https://polymc.org/)

   Установка [AUR](https://aur.archlinux.org/packages/polymc-bin) пакета

   ```sh
   yay -S polymc-bin
   ```

3. [MultiMC](https://github.com/MultiMC/Launcher)

   Установка [AUR](https://aur.archlinux.org/packages/multimc-bin) пакета

   ```sh
   yay -S multimc-bin
   ```

4. [ATLauncher](https://atlauncher.com/)

   Установка [AUR](https://aur.archlinux.org/packages/atlauncher-bin) пакета

   ```sh
   yay -S atlauncher-bin
   ```

5. [GDLauncher](https://gdevs.io/)

   Установка [AUR](https://aur.archlinux.org/packages/gdlauncher-bin) пакета

   ```sh
   yay -S gdlauncher-bin
   ```

## Сервера для Java версии

- [Для самой последней версии](https://www.minecraft.net/en-us/download/server)
- [Для всех остальных старых версий](https://gist.github.com/cliffano/77a982a7503669c3e1acb0a0cf6127e9)

## Запуск с нативным Wayland

На данный момент проверено на Legacy Launcher с "Рекомендуемой java-runtime-delta"

В Legacy Launcher открываем "Настройки лаунчера и игры" далее в Java/JRE жмём
"Настроить" в "Аргументы Java" вставляем

```txt
-Dorg.lwjgl.glfw.libname=/usr/lib64/libglfw.so
```

Сохраняем и теперь Minecraft будет запущен на нативном Wayland'е режиме
