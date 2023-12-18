+++
title = "Некоторые игры из Steam не определяют геймпад как Xbox360"
date = 2023-10-09T23:10:00+05:00
draft = false
[taxonomies]
categories = ["troubleshooting"]
tags = ["linux-gaming"]
+++

На примере The Binding of Isaac: Rebirth (Native - OpenGL)\
Геймпад 8BitDo Pro 2 Xinput по проводу

При включённом Steam Input внутри игры геймпад нормально не определяется, но работает\
Раскладка не та что должна быть, в общем беспорядок

![image](/images/gamepad-steam-not-identify-as-xbox/steam-input.png)

![image](/images/gamepad-steam-not-identify-as-xbox/issac-incorrect-layout-gamepad.png)

Если в свойствах игры Steam во вкладке отключить Steam Input (Отключить систему ввода Steam) тогда раскладка станет нормальной и автоматически подхватит управление

![image](/images/gamepad-steam-not-identify-as-xbox/correct-layout-gamepad.png)
