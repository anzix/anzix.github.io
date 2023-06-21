+++
title = "!Говерноры и профили для CPU или как повысить производительность без софта"
date = 2023-03-09T20:03:39+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Источники:
* https://losst.ru/chastota-protsessora-v-linux


Можно не пользоваться GUI раздутым софтом [CoreCtrl](https://docs.google.com/document/d/1c9yqKSz5LkS1Gd422w4TMdf-_76PHySzku4PsHbxZok/edit#heading=h.qqdpy2yl5x)

Воспользовался я простой функцией, находится в моём dotfiles [конфиге functions.zsh](https://gitlab.com/anzix/dotfiles/-/blob/7d2e8753a639145d40bae0a7299033c5bfc99315/zshrc/.config/zsh/functions.zsh#L82-94)
Вставить вы её можете в `.zshrc` или `.bashrc` сохранив файл и перезапустить Shell

Как юзать?
```sh
powermode [профиль]
```

Какой используется профиль?
```sh
powermode which
```

Доступные профили
```sh
powermode options
```

Профили:
* Дефолтный режим CPU: `schedutil`
* Экономия энергии: `powersave`
* Производительность: `performance`
* *Консервативный: `conservative`

> *Консервативный - при появлении нагрузки резко устанавливается самая высокая частота, а при снижении нагрузки частота медленно снижается

После того как выставил профиль performance, мой процессор постоянно работает на частоте 3900MHZ

Посмотреть частоту всех ядер процессора можете из данной команды <span style="color:green">(советую сделать alias из неё)</span>
```sh
watch -n 1 grep \'cpu MHz\' /proc/cpuinfo
```

## Автозагрузка профиля при старте системы

Прописываем `cpufreq.default_governor=conservative` параметр в конфиге ядра Grub
```sh
nvim /etc/default/grub
```
```sh
...
GRUB_CMDLINE_LINUX_DEFAULT="...cpufreq.default_governor=conservative"
...
```
И после перезагрузки частота процессора будет разогнанной при нагрузке

