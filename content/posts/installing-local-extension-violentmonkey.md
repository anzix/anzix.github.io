+++
title = "Как в расширении violentmonkey добавить локальный скрипт"
date = 2023-12-11T18:10:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["other"]
+++

- [Источник решения](https://violentmonkey.github.io/posts/how-to-edit-scripts-with-your-favorite-editor/)
- [Локальный JS Скрипт](https://rutracker.org/forum/viewtopic.php?p=71556284#71556284)

Необходимо было в расширениях дать доступ локально открывать файлы по URL ссылкам

![image](/images/installing-local-extension-violentmonkey/settings_browser.png)

При создании rutracker-script.js внутри которого [сам код](https://rutracker.org/forum/viewtopic.php?p=77455960#77455960) перетаскиваем прямо в браузер и копируем URL

Внутри violentmonkey жмём + и "Установить по ссылке" вставляя скопированную ссылку

![image](/images/installing-local-extension-violentmonkey/extension_popup.png)
