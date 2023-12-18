+++
title = "Открывать скачанные файлы из браузера через терминал alacritty+lf"
date = 2023-12-11T02:00:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Данный метод работает и на ranger

Создаём `file.desktop` в ``~/.local/share/applications/`` и вставляем в него

```conf
[Desktop Entry]
Type=Application
Name=File Manager
Exec=alacritty -e lf %u
NoDisplay=true
```

Далее сохраняем и потом редактируем ``~/.config/mimeapps.list`` и меняем

```conf
[Default Applications]
inode/directory=file.desktop
```

Браузер будет считывать mime лист и тот в свою очередь будет открывать терминал, а терминал с опцией **-e** открывает файловый менеджер LF или ranger
