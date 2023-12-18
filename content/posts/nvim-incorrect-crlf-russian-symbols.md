+++
title = "В vim/nvim некоректные символы при открытии файла с разными кодировками"
date = 2023-10-20T12:35:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux"]
+++

- [Список кодировок и решения](https://stackoverflow.com/a/38372767)

## Для файлов с кодировкой crlf нечитабельные русские символы

Вот как это выглядит

```txt
Èãðû êà÷àòü îòñþäà
```

Вот как выглядит локаль

```txt
LANG=ru_RU.UTF-8
LC_CTYPE="ru_RU.UTF-8"
LC_NUMERIC="ru_RU.UTF-8"
LC_TIME="ru_RU.UTF-8"
LC_COLLATE=C
LC_MONETARY="ru_RU.UTF-8"
LC_MESSAGES="ru_RU.UTF-8"
LC_PAPER="ru_RU.UTF-8"
LC_NAME="ru_RU.UTF-8"
LC_ADDRESS="ru_RU.UTF-8"
LC_TELEPHONE="ru_RU.UTF-8"
LC_MEASUREMENT="ru_RU.UTF-8"
LC_IDENTIFICATION="ru_RU.UTF-8"
LC_ALL=
```

Нашёл решение для vim и neovim в разных форматах vimscripts и lua

Редактируем конфиг файл neovim

```sh
nvim ~/.config/nvim/init.vim
```

Код в vimscripts

Самое важное в нём это set fileencodings=[значение кодировки] на который триггерится vim/neovim и преобразует некорректные символы в нормальный русский шрифт

```vim
set encoding=utf-8 | " Кодировка по дефолту
set termencoding=utf-8 | " Кодировка по дефолту
set fileformat=unix | " Формат файла по дефолту
set ffs=unix,dos,mac | " Формат файла, влияющий на окончания строк - будет перебирать в указанном порядке
set fileencodings=utf-8,cp1251 | " Список кодировок файлов для автоопределения
```

Код в lua (neovim)

```lua
vim.opt.encoding = "utf-8" -- Кодировка по дефолту
vim.opt.ffs = "unix,dos,mac" -- Формат файла, влияющий на окончания строк - будет перебирать в указанном порядке
vim.opt.fileencodings = "utf-8,cp1251" -- Список кодировок файлов для автоопределения
```

Теперь в Windows crlf текстовых файлов я смог нормально читать и редактировать файлы

А в nano вообще показываются одни ромбы с вопросом���\
Я пока не мог найти решение, да и я не заинтересован в нём.

## Для файлов с кодировкой cp936 нечитабельные китайские символы

Вот как это выглядит

![image](/images/nvim-incorrect-crlf-russian-symbols/incorrect-ch-symbols.png)

Для решения необходимо просто добавить в `fileencodings=[значение кодировки]` после utf-8 кодировку cp936, в конце не срабатывает

Пример neovim с lua конфигом

```lua
...
vim.opt.fileencodings = "utf-8,cp936,cp1251" -- Список кодировок файлов для автоопределения
...
```

После добавления cp936, все символы стали отображаться как надо

![image](/images/nvim-incorrect-crlf-russian-symbols/correct-ch-symbols.png)
