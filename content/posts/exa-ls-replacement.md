+++
title = "!Exa - замена ls команды"
date = 2023-03-26T13:23:43+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++
* https://the.exa.website/

Это замена ls команды для zsh который добавляет цвета и иконки. Делая наш терминал более приятным

> ТРЕБОВАНИЯ: У вас должен быть с установлен и настроен любой из семейства Nerd Font шрифт, смотрите [здесь](/posts/display-icons-in-terminal)
>
> **Не забывайте** что иконки юзают шрифты Nerd Fonts и для нормального отображения необходимо прописать шрифт в конфиге терминала kitty или alacritty как показано [тут](/posts/display-icons-in-terminal)

Установка exa
```sh
sudo pacman -S exa
```

![](/images/exa-ls-replacement/exa.png)

Для удобства из показанного скрина я сделал alias команды, вводим в `~/.zshrc`

```sh
# exa
if hash exa 2>/dev/null; then
	alias \
	 ls='exa -b --color=always --icons --group-directories-first' `# (-b) Понятные размеры файлов +Цвета +Иконки, Сгруппировано` \
	 ll='ls -l' `# +Подробно +листом вниз` \
	 la='ls -a' `# +Показ скрытых` \
	 lla='ls -la' `# +Подробно +Показ скрытых +листом вниз` \
	 lt="exa -aT --color=always --icons --group-directories-first -h --git-ignore --ignore-glob '.git|.gitignore|.DS_Store|node_modules'" `# Дерево (-h) Добавляет строку заголовка в каждый столбец` \
	 lt1="lt -L 1" \
	 lt2="lt -L 2" \
	 lt3="lt -L 3" \
	 l.='exa -d .* --group-directories-first' `# Показать только . (dot)`
else
# GNU `ls`
	alias \
	 ls='ls --color=auto --group-directories-first' \
	 ll='ls -l' `# +Подробно +листом вниз` \
	 la='ls -a' `# +Показ скрытых` \
	 lla='ls -la' `# +Подробно +Показ скрытых +листом вниз` \
	 lt='tree -aC --dirsfirst --gitignore -I ".git|.gitignore|.DS_Store|node_modules"' \
	 lt1='lt -L 1' \
	 lt2='lt -L 2' \
	 lt3='lt -L 3' \
	 l.='ls -d .*' `# Показать только . (dot)`
fi
```
