+++
title = "Вытащить обложку из трека (Mp3/FLAC)"
date = 2023-03-08T19:48:29+05:00
draft = false
[taxonomies]
categories = []
tags = ["Linux"]
+++

Источники
* https://unix.stackexchange.com/a/159571
* https://dalvikplanet.blogspot.com/2018/05/how-to-add-both-front-cover-and-back.html

Используя ffmpeg
```sh
ffmpeg -i file.mp3 -an -c:v copy file.jpg
```

Или используя `metaflac`
```sh
metaflac --export-picture-to=output-front.jpg file.flac
```
