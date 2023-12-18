+++
title = "Lgogdownloader - закачка оффлайн установщиков игр из GOG"
date = 2023-03-03T12:23:40+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

- https://github.com/Sude-/lgogdownloader

Качаем [aur пакет](https://aur.archlinux.org/packages/lgogdownloader)

```bash
yay -S lgogdownloader
```

И логинимся

```bash
lgogdownloader --login
```

Почему-то вылезла ошибка логина, хотя все реквизиты ввёл правильно

```sh
Login form contains reCAPTCHA (https://www.google.com/recaptcha/)
Try to login later or compile LGOGDownloader with -DUSE_QT_GUI=ON
HTTP: Login failed
```

Решил скачать [qt5 версию](https://aur.archlinux.org/packages/lgogdownloader-qt5)

```bash
yay -S lgogdownloader-qt5
```

Логинимся (при вводе пароля вылезает gog'овское окно логина)

```bash
lgogdownloader --enable-login-gui
```

И после него всё прошло успешно

Просмотреть список всех игр из библиотеки

```bash
lgogdownloader --list
```

```sh
~ ❯ lgogdownloader --list
Getting game names (2/2) 66 / 66
a_plague_tale_requiem_digital_goodie_pack
ad_2044
absolute_drift [1]
akalabeth_world_of_doom [1]
alders_blood_prologue [1]
arma_cold_war_assault
ascendant
beautiful_desolation_game
beneath_a_steel_sky [1]
bio_menace [1]
brigador [1]
builders_of_egypt_prologue
butcher
carmageddon_tdr_2000
cayne
....
```

<span style="color:gray">Серые</span> названия игр обозначают наличием только Windows порта.
<span style="color:green">Зелёные</span> с цифрой <span style="color:green">[1]</span> - это значит что у данной игры есть нативный порт под Linux т.е будет скачан установочный shell скрипт

Чтобы скачать Linux порт вводим (название игры должно быть таким-же)

```bash
lgogdownloader --platform=linux --download --game absolute_drift
```

Пояснение по основным опциям:

- `--platform=linux` - установщик игры будет под Linux
- `--exclude=extras` - исключает extra контент (мануалы, обои, саундтреки) из закачки
- `--download` - команда скачать
- `--game [название_игры]` - объяснению не нуждается

**Важно**: Место установщика который вы будете скачивать будет зависеть от того в каком каталоге вы находитесь, задать это можно вручную добавив опцию `--directory`

Пример: `--directory=/home/[user_name]/Games/Gog_installers`

После скачивания появится установочный shell скрипт `absolute_drift_4e17697_55731.sh`
Только перед запуском его необходимо изменить права доступа на исполнение через проводник или командной строкой

```bash
chmod +x [установочный shell скрипт]
```

Запускаем
![image](/images/lgogdownloader-archlinux/1667058827.png)

Чтобы скачать Windows установщик (для дальнейшего использования с wine'ом) убираем параметр `--platform=linux`

```bash
lgogdownloader --download --game absolute_drift
```

После скачивания wine'ом указывая префикс и следом путь к exe установщику, всё как обычно

## Исправление бага lgogdownloader при скачивании одной игры

- [Сурс откуда я брал псевдонимы](https://github.com/ssokolow/profile/blob/0e1bb67e1c83b58e2c49d89b06b1fd3928273614/home/.common_sh_init/aliases#L120-L137)
- [Сурс на мои подправленные псевдонимы Gitlab](https://gitlab.com/anzix/dotfiles/-/blob/master/base/zsh/.config/zsh/aliases.zsh#L125-140)

Когда пытаешься скачать установщик к примеру the_witcher, lgogdownloader зачем-то скачивает и второй Ведьмак (the_witcher_2) который я даже не указывал. Это сильно раздражает

![image](/images/lgogdownloader-archlinux/swappy-20221030-004734.png)
<span style="color:green">Исправить</span> это я нашёл только благодаря созданию псевдонимам (alias) и их использованию

Копируем и вставляем мои созданные псевдонимы в .zshrc или .bashrc

```bash
# lgogdownloader обвёртка
# Псевдонимы для более удобного синтаксиса argv загрузчика GOG
# Source: https://github.com/ssokolow/profile/blob/master/home/.common_sh_init/aliases

alias \
 gog="lgogdownloader" \
 gogl="lgogdownloader --list"

# Win установщик с бонусами
gogd() { local IFS=| lgogdownloader --retries=7 --download --game "^($*)\$";}
# Win установщик без бонусов
gogu() { local IFS=| lgogdownloader --retries=7 --download --exclude=extras --game "^($*)\$";}
# Linux установщик с бонусами
lgogd() { local IFS=| lgogdownloader --retries=7 --download --platform=linux --game "^($*)\$";}
# Linux установщик без бонусов
lgogu() { local IFS=| lgogdownloader --retries=7 --download --exclude=extras --platform=linux --game "^($*)\$";}
```

Теперь если юзать данные псевдонимы, lgogdownloader будет нормально воспринимать названия игры и будет выдавать то что вы написали, ничего лишнего
![image](/images/lgogdownloader-fix.png)
