+++
title = "Фикс ошибки декомпиляции apktool в андроид"
date = 2023-03-18T21:05:49+05:00
draft = false
[taxonomies]
categories = []
tags = ["anrdoid"]
+++

- [Github issue](https://github.com/iBotPeaches/Apktool/issues/1131)
- [Источник с фиксом данной проблемы](https://platinmods.com/threads/how-to-fix-apktool-decompile-error-using-mt-manager-app-arscdecoder-error.121708/)

При декомпиляции файла используя `apktool` я столкнулся с данной проблемой которою мог решить только на андроиде

```sh
apktool d framework-res.apk
```

Вывод с ошибкой

```
.....
I: Loading resource table...
Exception in thread "main" brut.androlib.AndrolibException: Multiple resources: spec=0x01030067 style/TextAppearance.StatusBar.EventContent, config=-v8
	at brut.androlib.res.data.ResType.addResource(ResType.java:50)
	at brut.androlib.res.data.ResType.addResource(ResType.java:44)
.......
```

Вот что мне потребовалось сделать для решения проблемы:

1. Копируем framework-res.apk на телеф
2. В Файловом менеджере ([MT Manager](https://4pda.to/forum/index.php?showtopic=548542)) открываем apk жмём "Открыть" откроется apk как zip архив
3. Выберите файл resource.arsc, он спросит, в каком редакторе открыть. Выберите "Редактор Arsc"
4. Нажмите в углу на три точки и сохраните, этим он перестроит файл .arsc и исправит ошибки.
5. Вернитесь назад и вам будет предложено обновите APK просто отметьте "Подпись" иии сделано!

После чего framework-res.apk можно будет декомпилировать и не будет никакой ошибки в apktool

Наглядная инструкция ввиде гифки на Android'е

![image](/images/fix-decompile-error-android-apktool/fix-for-decompile-apktool.gif)
