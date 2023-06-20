+++
title = "!GNU Stow - менеджер symlink’ов (Необходимо доработать)"
date = 2023-03-22T14:11:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++
* [Другой похожий гайд на ЛОРе](https://www.linux.org.ru/articles/admin/17146638)

Очень удобен в создании dot файлов
```sh
sudo pacman -S stow
```

Как пользоваться Gnu Stow и создать свой dotfiles

Проведем настройку git с добавлением пользователя (чтобы мы могли добавлять, коммитить и обновлять наш репозиторий не выходя из терминала)
```sh
git config --global user.email "твой@адрес.com"
git config --global user.name "имя или никнейм"
```
После этого мы можем спокойно делать всё что угодно с нашим репозиторием

Создаём начальные папки
```sh
mkdir -p dotfiles/base && cd dotfiles/base
```
```sh
# Пример имитации структуры файловой системы
# [любое_название]/[.config]/[точное_название_папки_конфига]/[сам_конфиг]
mkdir -p i3wm/.config/i3/
```
Вставляем данные конфиг файлы внутрь или копируем свои
```
cp ~/.config/i3/config ~/dotfiles/base/i3wm/.config/i3/
```
Создаём локальное репо
```sh
cd dotfiles
git init
git add --all
git commit -am "Creating dotfiles"
```
Создаём на github (или всякие подобные) пустое репо с названием dotfiles

> В настройках репо gitlab "Repository" - "Protected branches" необходимо поставить Allowed to force push

![](/images/GnuStow-guide/gitlab.png)

В терминале добавляем созданный url к локальному репо
```sh
git remote add origin https://gitlab.com/[NAME]/dotfiles.git
git branch -M main
git push -uf origin main
```
Всё))

## Действия по установке dotfiles

Скачиваем GNU Stow
```sh
sudo pacman -S stow
```
Клонируем репо и переходим в него
```sh
git clone --recurse-submodules https://gitlab.com/[NAME]/dotfiles && cd dotfiles/base
```
Опции:
* (n) - действия будут эмитироваться
* (v) - действия будут подробными
* (D) - удаляет симлинк
* (t) - целевая директория
* (/) - файлы по типу README.md будут игнорироваться
* (*) - stow будет вытягивать всё из папки dotfiles в целевую директорию
* (~) - HOME директория

```sh
# Вытащить всё
stow -vt ~ */
```
```sh
# Вытянуть отдельный конфиг
stow -vt ~ alacritty
```

```sh
# Удалить симлинк с директории
# Ошибки "BUG in find_stowed_path?"
# просто игнорируйте
stow -Dvt ~ alacritty
```

