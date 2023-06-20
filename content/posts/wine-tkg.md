+++
title = "Wine-tkg - самый пропатченный пользователями Wine"
date = 2023-03-03T12:04:11+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux Gaming"]
+++
* https://github.com/Frogging-Family/wine-tkg-git

Процесс установки прост
```bash
mkdir ~/Repos && cd ~/Repos
git clone https://github.com/Frogging-Family/wine-tkg-git.git
cd wine-tkg-git/wine-tkg-git/
```

Перед компиляцией на ваше усмотрение можно править настройки файла **customization.cfg** чтобы выжать доп-ную производительность.
Если лень читать и разбираться но выжать производительность хочется можно обратится на данные ресурсы указанные ниже:
[ARU (Wine-TKG)](https://ventureo.codeberg.page/source/linux-gaming.html#wine-tkg)

Если готовы тогда начинаем компиляцию (в моём случае с правкой **customization.cfg** это заняло больше 1 часа)
```bash
makepkg -sric
```

На все вопросы просто тыкаете Enter
## Свежий скомпилированный Wine-tkg
Идёте на [github страницу](https://github.com/Frogging-Family/wine-tkg-git/actions/workflows/wine-arch.yml) и выбираете по дате самый последний с отмеченной зелённой галочкой

![](/images/wine-tkg/Screenshot_20230117_015558.png)

Опускаетесь вниз до Artifacts и кликаете на "wine-tkg-build" для скачивания

Или скачать его по [ссылке](https://nightly.link/Frogging-Family/wine-tkg-git/workflows/wine-arch/master/wine-tkg-build.zip)

Распаковываете и устанавливаете используя pacman

```bash
sudo pacman -U fsync-git-version-x86_64.pkg.tar.zst
```
