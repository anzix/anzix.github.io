+++
title = "!Настройка Getty, автологин и т.д"
date = 2023-04-15T20:05:17+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

Мануал по всем опциям, если вдруг вам необходимо знать что делает каждая опция

Пояснение, коротко `man agetty`:

Описание:
```
agetty [options] port [baud_rate...] [term]
```
Опции [options]:
* `-a`, `--autologin $USER` - Входит в систему указанного пользователя, не спрашивая имя пользователя или пароль.
* `-n`, `--skip-login` - Не будет показывать пользователю его login, используется в связке с опцией --autologin указанной выше
* `-N`, `--nonewline` - Не печатает новую строку перед записью в файл /etc/issue
* `-i`, `--noissue` - Не отображать содержимое /etc/issue (или другого) перед написание приглашения для входа.
* `-J`, `--noclear` - Не очищать экран до запроса. По умолчанию экран очищается.
* `-p`, `--login-pause` - Ожидает ввода любой клавиши, прежде чем перейти к prompt для входа в систему. Возможно в сочетании с --autologin для экономии памяти путем ленивого создания shell.
* `-f`, `--issue-file path` - Указывает разделенный знаком ":" список файлов и каталогов, отображается вместо /etc/issue (или другого)

## Автовход без ввода логина и пароля (небезопасно)

А чтобы у нас постоянно не просили постоянно при загрузке логин и пароль (Что отлично комбинируется с startx), нужно отредактировать строку
```sh
sudo nano /etc/systemd/system/getty.target.wants/getty@tty1.service
```
Ищем в столбце **[Service]**
```sh
ExecStart=-/sbin/agetty -o '-p -- \\u' --noclear %I $TERM
```

## !Чтобы был только ввод логина
```
ExecStart=-/sbin/agetty -o $USER %I
```

## !Чтобы не было ввода пароля

```
ExecStart=-/sbin/agetty --autologin $USER --noclear %I $TERM
```

## Чтобы не было output лога при старте и выходе

![](/images/getty-autologin-and-etc/output-log-getty.png)


В `.zprofile` в конце startx вводим значение **`>/dev/null 2>&1`** после чего перезагружаемся
<pre>
if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
  startx <b>/dev/null 2>&1</b>
fi
</pre>
