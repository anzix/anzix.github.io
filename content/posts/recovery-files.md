+++
title = "Testdisk & Photorec - Востановление файлов в Linux"
date = 2023-03-02
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Восстановление удалённых данных с помощью testdisk

[Guide по использованию ENG](https://www.pandorarecovery.com/blog/recover-data-from-sd-card-on-linux/)

Качаем пакет testdisk содержащий в себе 2 инструмента, testdisk и photorec

```bash
sudo pacman -S testdisk
```

Использование в терминале:

```bash
testdisk # или photorec
```

Лично мне photorec помогла восстановить удалённый по случайности важный
файл на ntfs диске, поэтому я могу на все 100% рекомендую данное ПО

Но касательно восстановления данных на ext4 диске, не могу сказать так как я это
не проверял
