+++
title = "Всё что связано с отладкой используя gdb, debuginfod"
date = 2024-07-27T23:50:00+05:00
draft = false
[taxonomies]
categories = ["programming"]
tags = ["C"]
+++

В данном руководстве я буду использовать мой проект на Си `inventory` из книжки\
"C Programming A Modern Approach 2nd Ed от King, K. N" (Глава 16, страница 391) в\
качестве подопытного.

## Понятие отладочная информация

Для чего нужны отладочные символы?

Отладочные символы в основном полезны для получения трассировок (backtrace)\
отладки для устранения неполадок во время выполнения.

О том что выбрать в компиляторе gcc, флаг `-g` или `-g1`?

- Флаг `-g` используется для генерации отладочной информации (с elf объекта) в\
  формате Dwarf, который является форматом по умолчанию, используемым GCC. Этот\
  формат включает информацию об исходном коде, такую как номера строк, имена\
  переменных и типы. Отладочная информация хранится в исполняемом файле, что\
  позволяет отлаживать программу с помощью таких инструментов, как GDB.

  Компиляция с данным флагом для крупных проектов **может быть как правило очень\
  медленным**

- Флаг `-g1` флаг который генерирует минимальную отладочную информацию. Он похож\
  на `-g`, но он содержит только самую важную информацию, такую как **таблица номера\
  строк, описания функций и внешних переменных**. Но не будет информации о локальных\
  переменных. Это уменьшает размер отладочной информации, делая исполняемый файл меньше.

  Файл `inventory` без отладочной информации, а `inventory-debug` с флагами отладки\
  `-g3`. Что является максимальная отладочная информация и вот как значительно весят\
  два разных исполняемых файла

  ```txt
  .rwxr-xr-x  16Ki anix 14 июл 16:04  inventory
  .rwxr-xr-x  49Ki anix 14 июл 16:05  inventory.debug
  ```

  Вот как весит файл `inventory-debug` с флагом `-g1`

  ```txt
  .rwxr-xr-x  18Ki anix 14 июл 16:10  inventory.debug
  ```

Что за флаг `-ggdb<уровень>`?

- `-ggdb<уровень>`: Создавать отладочную информацию для использования GDB. Это\
   означает использование наиболее выразительного доступного формата (DWARF, st\
   abs или собственный формат, если ни один из них не поддерживается),\
   включая расширения GDB, если это вообще возможно.

  > Для совместимости с использованием другого отладчика как Valgrind лучше всего\
  > ограничится `-g1`

## Плохая отладка программы

Вот как выглядит плохой способ отладки при получения трассировки (backtrace)

Запускаем отладчик `gdb /usr/bin/inventory`, записываем логи `set logging enabled on`,\
запускаем программу `run`. Найдите pid запущенной программы используя\
`ps aux | grep inventory` и убейте данный процесс `kill -SIGSEGV pid`

После чего выводим полную трассировку `thread apply all bt full`, и вот что мы видим

```txt
Program received signal SIGSEGV, Segmentation fault.
0x00007ffff7ea4981 in read () from /usr/lib/libc.so.6
(gdb) thread apply all bt full

Thread 1 (Thread 0x7ffff7d99740 (LWP 1013355) "inventory"):
#0  0x00007ffff7ea4981 in read () from /usr/lib/libc.so.6
No symbol table info available.
#1  0x00007ffff7e25e63 in _IO_file_underflow () from /usr/lib/libc.so.6
No symbol table info available.
#2  0x00007ffff7e282c2 in _IO_default_uflow () from /usr/lib/libc.so.6
No symbol table info available.
#3  0x00007ffff7e00618 in ?? () from /usr/lib/libc.so.6
No symbol table info available.
#4  0x00007ffff7df3a06 in __isoc99_scanf () from /usr/lib/libc.so.6
No symbol table info available.
#5  0x000055555555507e in ?? ()
No symbol table info available.
#6  0x00007ffff7dc1c88 in ?? () from /usr/lib/libc.so.6
No symbol table info available.
#7  0x00007ffff7dc1d4c in __libc_start_main () from /usr/lib/libc.so.6
No symbol table info available.
#8  0x0000555555555145 in ?? ()
No symbol table info available.
```

Н-И-Ч-Е-Г-О, это по причине того что нету отладочной информации (об этом\
свидетельствует "No symbol table info available"). Используйте **хорошую
отладку программы**.

## Хороший способ отладки: Используя debuginfod (рекомендуемый)

- [Arch Wiki Debuginfod](https://wiki.archlinux.org/title/Debuginfod)
- [Новость о добавлении debuginfod в Arch Linux 2022](https://archlinux.org/news/debug-packages-and-debuginfod/)
- [KDE инструкция о полезной отладке используя debuginfod](https://community.kde.org/Guidelines_and_HOWTOs/Debugging/How_to_create_useful_crash_reports#Debuginfod)
- [RedHat Blog представление Debuginfod](https://developers.redhat.com/blog/2019/12/17/deploying-debuginfod-servers-for-your-developers)
- [Alt Linux Wiki](https://www.altlinux.org/Debuginfo)
- [Ubuntu Docs](https://ubuntu.com/server/docs/about-debuginfod)
- [Официальная страница debuginfod](https://sourceware.org/elfutils/Debuginfod.html)
- [Отладка AppImage используя debuginfod](https://github.com/KDAB/hotspot/blob/master/HACKING.md#debugging-the-appimage)

TL;DR: debuginfod предоставляет веб-сервер отладочной информации без установки\
отладочных пакетов.

Подробное описание: debuginfod будет предоставлять отладочные данные по запросу в\
соответствии с запросами GDB/Valgrind и другого программного обеспечения для отладки.\
Debuginfod будет прозрачно извлекать/собирать отладочные данные и исходный код по мере\
необходимости во время отладки с помощью программного обеспечения, поддерживаемого debuginfod.

У каждого дистрибутива Linux есть свой сервер debuginfo которые обслуживает\
отладочные символы, например у меня на Arch Linux уже присутствует указанный\
https сервер Arch Linux. Достаточно просто глянуть `$DEBUGINFOD_URLS` переменную\
чтобы убедится.

```txt
$ echo $DEBUGINFOD_URLS
https://debuginfod.archlinux.org
```

Это очень полезно как для отладки своего ПО так и просто помощь людям\
которым вы хотите помочь.

Приступим

1. Есть два варианта:

   - Если столкнулись с SEGFAULT и вы хотите помочь отладить стороннего ПО:\
     Вызываем отладчик используя `coredump` команду: $ `coredumpctl debug PID`\
     Для поиска **PID** вызовите `coredumpctl list`

   - Если же вы отлаживаете своё ПО: То просто вызываете `gdb путь/до/исполняемого`

2. Если спрашивает **`Enable debuginfod for this session? (y or [n])`** обязательно\
   соглашаемся на `y` (это может использовать до нескольких сотней МБ трафика для\
   загрузки файлов отладочной информации)

3. Если возникает это жмём `c` и Enter

   ```txt
   --Type <RET> for more, q to quit, c to continue without paging--
   ```

4. По умолчанию если включить логирование то название будет `gdb.txt`, если оно\
   вам не нравиться то вы можете использовать команду `(gdb) set logging file your_file_name.log`\
   После этого включить логирование в файл `(gdb) set logging enabled on`

5. Запускаете отслеживание (backtrace) `(gdb) bt` или запускаете программу\
   `(gdb) run` (может долго запускаться потому-что загружает отладочную\
   информацию)

6. В случае если программа работает и в какой-то момент останавливается\
   (например `kill -SIGSEGV pid`) запустите `(gdb) thread apply all bt full`,\
   после окончания вы можете отключить логирование `(gdb) set logging enabled off`.\
   И выходим на `q` и соглашаемся выйти на `y`

7. Теперь если вы получили лог файл который вы хотите отправить разработчикам, вы\
   можете отредактировать в нём любую личную информацию, если она слишком длинная,\
   поместите ее в любой сервис `pastebin` и поделитесь ссылкой

## Вот как используется debuginfod в инфраструктуре Arch Linux

Для официальных пакетов в репозиториях **Core** и **Extra**

1. Собирается пакет с опцией **debug** (выставлен в PKGBUILD)
2. При сборке **обычный пакет (содержащий executable)** и **`*-debug` (содержащий\
   debuginfo)** пакет отправляется в сервер debuginfod который индексирует отладочную\
   информацию.
3. В вашем дистрибутиве по умолчанию будет указан свой сервер debuginfod (для Arch Linux это\
   `https://debuginfod.archlinux.org`) откуда берётся и извлекается отладочная\
   информация во время выполнения отладчика используя gdb/Valgrind.
4. Пользователю (клиенту) можно сразу отлаживать программу и она сразу всю\
   отладочную информацию скачает.
5. Поэтому для клиентов не имеет смысл качать пакет `*-debug` (хоть это возможно),\
   так как они уже [индексированы на наличие отладочной информации](https://debuginfod.archlinux.org/packages).
6. (Опционально): Для улучшения поиска отладочной информации стоит ещё добавить\
   URL сервера debuginfod помимо Arch Linux через пробел:

   ```sh
   export DEBUGINFOD_URLS="https://debuginfod.archlinux.org ДРУГОЙ_URL_DEBUGINFO_1 ДРУГОЙ_URL_DEBUGINFO_2"
   ```

   [Список всех доступных публичных серверов указаны тут](https://sourceware.org/elfutils/Debuginfod.html)

Для неофициальных пакетов (AUR):

В то время как **наше ПО поставляется неофициально, в виде пакета AUR**,\
в установке `*-debug` пакета имеет смысл. Так как это единственный удобный\
вариант отладки и получения отладочных символов (debug symbols), особенно для\
добровольцев кто хочет помочь развивать ваше ПО.

## Отладочный пакет `*-debug` для неофициальных пакетов в Arch Linux (для разработчиков)

- [Рекомендации по пакету Arch](https://wiki.archlinux.org/title/Arch_package_guidelines)
- [Arch Wiki Debugging](https://wiki.archlinux.org/title/Debugging/Getting_traces#Compilation_options)

Как было сказано, помимо обычного пакета `inventory` следом идёт отладочный пакет\
`inventory-debug` добавленными отладочными символами

Нужно указать в PKGBUILD опцию `debug` чтобы создавался `inventory-debug`\
пакет вместе с обычным пакетом.

```PKGBUILD
options=('debug')
```

> Если вы спросите "нужно ли менять секцию `build()` тип компиляции команды\
> `make` на `debug`?", ответ "Нет, не нужно". Потому-что при включённой **опции\
> `debug`** используются флаги `DEBUG_CFLAGS` и `DEBUG_CXXFLAGS` из конфиг\
> файла `/etc/makepkg.conf`. Собственно добавляются в `CFLAGS` и используются\
> компилятором `gcc`, [это описано здесь](https://wiki.archlinux.org/title/Debugging/Getting_traces#Compilation_options).\
> ОДНАКО (для больших проектов), вы можете переопределить флаги отладки в основном только для\
> пакетизации, например в Arch Linux файле PKGBUILD переопределить флаги отладки\
> `DEBUG_CFLAGS` или `DEBUG_CXXFLAGS` на свои (`DEBUG_CFLAGS=" -g1"`) для\
> **генерации минимальных отладочных файлов**. Это может значительно ускорить\
> компиляцию и уменьшить размер этих данных для вносчиков *и позволит упаковать\
> данные в `debuginfod`"* (не понял, что это значит? FIXME)

Раз речь зашла про PKGBUILD то при отладке рекомендуется использовать опцию\
`check` которая активирует секцию `check()` (если она присутвтвует)

Ничего больше изменять/добавлять в PKGBUILD не нужно. Тепепрь собираем пакет\
используя команду `makepkg -sric`

Сборка с отладочным пакетом будет идентично с тем как поставляются пакеты в\
[отдельном зеркале](https://geo.mirror.pkgbuild.com/extra-debug/os/x86_64/)

Символы отладки после установки пакета находятся в `/usr/lib/debug/usr/bin/inventory.debug`\
Теперь можно использовать gdb для отладки моего ПО.

> Из выхлопа ниже, обратите внимание, что отладочная информация для `/usr/bin/inventory`\
> загрузилась также из `/usr/lib/debug/usr/bin/inventory.debug`. Файлы с отладочной\
> информацией хранятся в дереве `/usr/lib/debug/`. Это довольно удобно

```txt
gdb /usr/bin/inventory
Reading symbols from /usr/bin/inventory...
Reading symbols from /usr/lib/debug/usr/bin/inventory.debug...
(gdb) b main
Breakpoint 1 at 0x1020: file src/inventory.c, line 35.
(gdb) run
Starting program: /usr/bin/inventory

This GDB supports auto-downloading debuginfo from the following URLs:
  <https://debuginfod.archlinux.org>
Enable debuginfod for this session? (y or [n])
Debuginfod has been disabled.
To make this setting permanent, add 'set debuginfod enabled off' to .gdbinit.
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".

Breakpoint 1, main () at src/inventory.c:35
35      {
(gdb) list
30       * Повторяется, пока пользователь не введёт команду <q>.
31       * Печатает сообщение об ошибке, если пользователь вводит
32       * недопустимый код.
33      */
34      int main(void)
35      {
36              char code;
37
38              for (;;) {
39                      printf("Введите код операции: ");
```

## Как локально проверить отладочный `*-debug` пакет через debuginfod?

- [Ручное добавление отладочных символов в Non-Debug сборке](https://zakuarbor.github.io/blog/debugging-symbols/) - Полезное чтиво
- [Создание отдельного файла с символами отладки](https://stackoverflow.com/questions/866721/how-to-generate-gcc-debug-symbol-outside-the-build-target)
- [Работа с огромными отладочными символами](https://interrupt.memfault.com/blog/dealing-with-large-symbol-files) - Полезное чтиво
- [Поставка отладочной информации, разные методы включая debuginfod](https://maskray.me/blog/2022-10-30-distribution-of-debug-information) - **Рекомендуется прочитать**
- [debuginfod demo: get debugging data + sources easily - DevConf.CZ 2021](https://youtu.be/X4Eg7vfPNPY?si=aR6B7lqZtJeZYT-u&t=703)
- [Ubuntu Summit 2022 | Debuginfod in Ubuntu](https://www.youtube.com/watch?v=q9buD4Vvp2I)
- [man objcopy](https://man.archlinux.org/man/objcopy.1.en)
- [man eu-srcfiles](https://man.archlinux.org/man/core/elfutils/eu-srcfiles.1.en)
- [man eu-readelf](https://man.archlinux.org/man/eu-readelf.1.en)
- [man dwarfdump](https://man.archlinux.org/man/extra/libdwarf/dwarfdump.1.en)

В данном примере я покажу как работает debuginfod используя сборку AUR пакета с\
**debug** опцией и созданием своего локального сервера, чтобы я сам мог локально\
получить отладочную информацию своего ПО используя `debuginfod-find`

Устанавливаем пакет (который содержит `debuginfod-find`)

```sh
sudo pacman -S debuginfod
```

**Настройка серверной части debuginfod**:

Удаляем пути debuginfod upstream сервер Arch Linux, чтобы убедится что\
запускается и используется сервер локально а НЕ сервер Arch Linux

```sh
export DEBUGINFOD_URLS=
```

Перемещаем **собранный обычный и debug пакет в отдельную папку**

Запускаем debuginfod сервер на индексирование исполняемых/исполняемого\
файлов, анализ и записи отладочной информации и исходников

```sh
# Если находитесь внутри папки с пакетами, можете использовать `-F .` (точку) как текущий каталог
debuginfod -v -Z .tar.zst=zstdcat -F путь/где/лежат_архивы_пакетов/
```

Пояснения по опциям `debuginfod`:

- `-v`: Увеличьте подробности протоколирования в стандартном дескрипторе\
   файла ошибок. Можно повторить для увеличения детализации. Подробность\
   по умолчанию равна 0.
- `-F`: Активирует сканирование файлов ELF/DWARF. По умолчанию отключено.
- `-Z`: Активируйте дополнительный шаблон при сканировании архива. Файлы\
   с расширением EXT (включая точку) будут обработаны. Например\
   `-Z .tar.zst=zstdcat` сообщает ему использовать `zstdcat` для обработки\
   файла `.tar.zst` (например, пакета Arch Linux).\
   Для другого типа архива, например `tar.lz4` это должно быть так: `-Z .tar.lz4=lz4cat`

**Для клиента (тот кто будет брать отладочную информацию для отладки ПО)**:

```sh
# Если вы хотите точно видеть, какие сетевые запросы выполняются и какие
# кэшированные файлы используются, вы можете установить
export DEBUGINFOD_VERBOSE=1 # Предупреждение: очень подробно
# Если вы хотите видеть диагностику хода выполнения во время загрузки
export DEBUGINFOD_PROGRESS=1
# Необходимо если вы испытываете очень медленное ожидание в попытках поиска
# файла используя debuginfod-find. Эта переменная управляет временем ожидания
# начала загрузки для каждого HTTP-соединения debuginfod. Сервер, который не может
# предоставить хотя бы 100 КБ данных в течение этого количества секунд, пропускается.
# Значение по умолчанию — 90 секунд. (Ноль или отрицательное значение означает «нет тайм-аута».)
export DEBUGINFOD_TIMEOUT=5
# ОБЯЗАТЕЛЬНО: Выставляем URL сервера на локальный для локальных проектов, тем самым только
# у нас будет хранится отладочная информация
# По умолчанию debuginfod прослушивает порт 8002
export DEBUGINFOD_URLS="http://localhost:8002"
```

Вопрос: Как debuginfod находит отладочные символы для двоичного файла, который\
я отлаживаю?

Ответ: `debuginfod` использует уникальный хэш, который идентифицирует двоичные\
файлы и разделяемые библиотеки, называемый Build-ID. Этот 160-битный хэш SHA-1\
генерируется компилятором, и с ним можно ознакомиться с помощью таких инструментов,\
как:

```sh
# readelf
readelf -n /usr/bin/inventory | awk '/ID сборки:/ {print $3}'

# eu-readelf
eu-readelf -n /usr/bin/inventory | awk '/Build ID:/ {print $3}'
```

И можно узнать исходники CU (Compilation Unit) разделяемой библиотеки командой.

```sh
eu-srcfiles -c -e /usr/bin/inventory
```

Основной выхлоп от `readelf`

```txt
6a9952fbf6e6d429fb07ac48f5e1c80cb323037a
```

**ID сборки** это и есть **Build-ID**, хеш двоичного файла

**ВАЖНО**: **Пакет `inventory-debug` не скачиваю** специально для того чтобы я\
мог протестировать `debuginfod` локальный сервер в котором этот самый\
`inventory-debug` пакет (и обычный) и индексируются

Запрос собранной информации по **Build-ID** от сервера debuginfod можно использовать\
команду `debuginfod-find`

При запросе важно чтобы размер файла не должен быть пустым. Находиться это будет\
в `~/.cache/debuginfod_client/Build-ID/Запрошенная_информация`

```sh
# Для начало убедимся что мы получим тот самый Build-ID от debuginfo
debuginfod-find debuginfo /usr/bin/inventory # Запрос только отладочной информации из исполняемого
/home/anix/.cache/debuginfod_client/6a9952fbf6e6d429fb07ac48f5e1c80cb323037a/debuginfo # ДА, это он самый!
debuginfod-find executable 6a9952fbf6e6d429fb07ac48f5e1c80cb323037a # Запрос только исполняемого файла используя Build-ID
/home/anix/.cache/debuginfod_client/6a9952fbf6e6d429fb07ac48f5e1c80cb323037a/executable # Да! Есть запрос!
# Теперь запрос исходников (для проверки будет 2), указываю путь такой который
# является структурой для debug пакета (обычно это например /usr/src/debug/package_name/executable_name/src/source.c)
debuginfod-find source 6a9952fbf6e6d429fb07ac48f5e1c80cb323037a /usr/src/debug/inventory-git/inventory/src/inventory.c # Первый исходник
/home/anix/.cache/debuginfod_client/6a9952fbf6e6d429fb07ac48f5e1c80cb323037a/source##usr##src##debug##inventory-git##inventory##src##inventory.c # Да!
debuginfod-find source 6a9952fbf6e6d429fb07ac48f5e1c80cb323037a /usr/src/debug/inventory-git/inventory/src/readline.c # Второй исходник
/home/anix/.cache/debuginfod_client/6a9952fbf6e6d429fb07ac48f5e1c80cb323037a/source##usr##src##debug##inventory-git##inventory##src##readline.c # Да!
```

Видно что файл отсканирован на предмет данных которые находятся в `~/.cache/debuginfod_client/`,\
например hash код `6a9952fbf6e6d429fb07ac48f5e1c80cb323037a` будет вытягивать отладчик\
gdb/Valgrind если будет натыкаться на отсутствие отладочной информации.

Отлаживаем файл используя gdb, при вопросе о использовании debuginfod на\
адресс `http://localhost:8002` отвечаем `y`

```sh
gdb /usr/bin/inventory
```

Вот выхлоп

```txt
$ gdb /usr/bin/inventory
Reading symbols from /usr/bin/inventory...

This GDB supports auto-downloading debuginfo from the following URLs:
  <http://localhost:8002>
Enable debuginfod for this session? (y or [n]) y
Debuginfod has been enabled.
To make this setting permanent, add 'set debuginfod enabled on' to .gdbinit.
Reading symbols from /home/anix/.cache/debuginfod_client/6a9952fbf6e6d429fb07ac48f5e1c80cb323037a/debuginfo...
(gdb)
```

Как видно, первое это прочтены символы исполняемого файла, затем после включения\
debuginfo сразу же запросил файд содержащий только отладочные символы (debug symbols)

Отлаживаем файл используя Valgrind (в разы полезнее)

```sh
valgrind --leak-check=full --track-origins=yes -s --show-leak-kinds=all --max-stackframe=8016174317795404487 /usr/bin/inventory
```

> TODO: У меня почему-то valgring не может локально без debug пакета выполнить\
> отладку. Если изменить переменную DEBUGINFOD_URLS на\
> `export DEBUGINFOD_URLS="http://localhost:8002"`\
> То он просто не может по причине неправильно выставленного URL. Необходимо расследование.

Что очень круто, так это то что debuginfod может скачивать отладочную информации\
и исходный код для нескольких версий вашего ПО

Отключение сервера `debuginfod`

```sh
unset DEBUGINFOD_URLS
```

Вы также можете включить/отключить его исключительно в GDB, добавив следующую\
команду в свой `.gdbinit` или используя ее перед началом сеанса отладки:

```txt
set debuginfod enabled off
```

## Что же на самом деле делает опция debug в PKGBUILD при сборке?

По сути производится две основные команды: `objcopy` и `strip`

objcopy - копирует и переводит объектные файлы

В данный момент я покажу всё кроме включения исходников, которые намного легче это\
делает простая опция `debug` в PKGBUILD

```sh
# Сделал копию исполняемого содержащий только отладочную информацию
objcopy --only-keep-debug inventory inventory.debug
# Сделал копию исполняемого с обрезанной отладочной информацией
strip -S inventory -o inventory.stripped
# Просматриваем Build-ID и копируем его
eu-readelf -n inventory | awk '/Build ID:/ {print $3}'
# e9d865b4869aac5bc45a0b086a73c4d3a2e4aa74
# Завернул эти два файла в zstd архив и переместил в удобную для сервера папку
tar cf inventory.tar.zst --zstd inventory.stripped inventory.debug
mv inventory.tar.zst debuginfo/
# Запускаю север debuginfod для извлечения отладочной информации архива
debuginfod -v -Z .tar.zst=zstdcat -F путь/до/архива.tar.zst/

# Выставляем URL сервера на локальный для клиентского запроса
export DEBUGINFOD_URLS="http://localhost:8002"
# При поиске используя debuginfod-find я могу получить executable и debuginfo
# Вставляем скопированный Build-ID
debuginfod-find debuginfo e9d865b4869aac5bc45a0b086a73c4d3a2e4aa74 # Запрос только отладочной информации
debuginfod-find executable e9d865b4869aac5bc45a0b086a73c4d3a2e4aa74 # Запрос только исполняемого файла
```

## Различные лайфхаки gdb

1. Saving Breakpoints

   To save breakpoints, you can use the `save breakpoints [filename]` in GDB.

   - Start by setting some breakpoints in your code

     ```txt
     gdb -q ./my_program
     (gdb) break main
     (gdb) break some_function
     ```

   - Save the breakpoints to a file

     ```txt
     (gdb) save breakpoints my_breakpoints.txt
     ```

2. Loading Breakpoints

   To load breakpoints from a file, you can use the `source [filename]` in GDB.

   - Start a new debugging session

     ```txt
     gdb -q ./my_program
     ```

   - Load the breakpoints from the file

     ```txt
     (gdb) source my_breakpoints.txt
     ```

3. Загрузка файла с отладочными символами вручную (вряд-ли пригодиться)

   Создание файла только с отладочными символами можно использовать `objcopy`

   ```sh
   objcopy --only-keep-debug inventory inventory.debug
   ```

   Выполняем gdb и загружаем файл символов используя `symbol-file <файл.debug>`\
   команду

   ```sh
   Reading symbols from inventory.stripped...
   (No debugging symbols found in hello.debug.stripped)
   >>> symbol-file inventory.debug  # используя эту команду для загрузки файла символов
   Reading symbols from inventory.debug...
   ```
