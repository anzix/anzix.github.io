+++
title = "Snapper - бэкап файловой системы Btrfs"
date = 2023-03-02T12:59:48+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Обновлено: 20.12.23

Видео гайды:
[Первый](https://www.youtube.com/watch?v=Xynotc9BKe8&t=927s)
[Второй](https://www.youtube.com/watch?v=sm_fuBeaOqE)
[Третий](https://www.youtube.com/watch?v=_97JOyC1o2o)

Список пакетов для btrfs и snapper

- snapper - основной пакет
- snap-pac - делает снапшоты после каждой установки/обновления/удаления пакетов Pacman
- grub-btrfs - добавляет grub меню снимков сделанных snapper'ом чтобы в них загружаться
- inotify-tools - необходимая зависимость для grub-btrfs
- snap-sync - использовать снимки Snapper для резервного копирования на внешний диск
- [snap-pac-grub](https://aur.archlinux.org/packages/snap-pac-grub) - дополнительно обновляет записи GRUB для grub-btrfs после того, как snap-pac сделал снимки
- [snp](https://aur.archlinux.org/packages/snp) - заворачивает любую shell команду и создаёт снимок до выполнения этой команды (snp sudo pacman -Syu)
- [snapper-rollback](https://aur.archlinux.org/packages/snapper-rollback) - откат системы который соответствует [схеме разметки Arch Linux](https://wiki.archlinux.org/index.php/Snapper#Suggested_filesystem_layout)
- [btrfs-assistant](https://aur.archlinux.org/packages/btrfs-assistant/) - Gui программа обслуживания файловой системы Btrfs и для создания снимков

Однако в текущем руководстве я буду использовать те что указаны в данной команде

```bash
yay -S snapper snap-pac grub-btrfs snp
```

Входим в root оболочку

```bash
sudo -s
```

Размонтируем `.snapshots`

```bash
umount /.snapshots
rm -rf /.snapshots
```

Создаём конфиг Snapper

```bash
snapper -c root create-config /
```

> **Примечание**: Если производите в режиме chroot используйте `--no-dbus` опцию

Удаляем subvol .snapshots Snapper

```bash
btrfs subvolume delete /.snapshots
```

Повторно создаём и монтируем /.snapshots

```bash
mkdir /.snapshots
mount -a
```

Меняем права доступа для легкой замены снимка @ в любое время без потери снимков snapper.

```bash
chmod -v 750 /.snapshots
```

Доступ для пользователей без полномочий root

```bash
chown -R -v :wheel /.snapshots
```

## Настройка snapper для снимков /

Устанавливаю лимит снимков

```bash
sed -i "s|^TIMELINE_LIMIT_HOURLY=.*|TIMELINE_LIMIT_HOURLY=\"5\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_DAILY=.*|TIMELINE_LIMIT_DAILY=\"7\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_WEEKLY=.*|TIMELINE_LIMIT_WEEKLY=\"0\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_MONTHLY=.*|TIMELINE_LIMIT_MONTHLY=\"0\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_YEARLY=.*|TIMELINE_LIMIT_YEARLY=\"0\"|g" /etc/snapper/configs/root
```

Включаем службы Snapper

```bash
systemctl enable snapper-timeline.timer
systemctl enable snapper-cleanup.timer
```

Создаём первоначальный снимок

```bash
snapper -c root create --description "Backup"
```

Обновляем записи снимков grub

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Plocate не показывает индексы найденых файлов если используется файловая система Btrfs

- [Источник](https://devctrl.blog/posts/plocate-not-a-drop-in-replacement-if-you-re-using-btfrs/)

Данная правка конфига исправляет это

```sh
sed -i 's/PRUNE_BIND_MOUNTS =.*/PRUNE_BIND_MOUNTS = "no"/' /etc/updatedb.conf
```

Предотвращение индексирования снимков программой "updatedb"\
Что замедляло бы работу системы при использовании locate/plocate

```bash
sed -i '/PRUNEPATHS/s/"$/ \/\btrfsroot \/\.snapshots"/' /etc/updatedb.conf
```

Готово, всё настроено и можно выходить из root оболочки

## Как откатить установленный пакет

Как только вы установили какой-либо пакет, можно вернуться назад до того как этот пакет был установлен

Например:

```bash
sudo pacman -S cmatrix
```

Смотрим то что snapper сделал pre (до установки пакета) номер 40 и post снимок (после установки пакета) под номером 41

```bash
snapper ls
```

Чтобы вернуть состояние до установки пакета т.е pre, вводим

```bash
sudo snapper undochange 40..41
```

И cmatrix теперь нету

Чтобы вернуть состояние после установки пакета (post) вводим в обратном порядке

```bash
sudo snapper undochange 41..40
```

И cmatrix снова появился

Увидеть изменения pre и post можно командой

```bash
sudo snapper status 41..40
```

## Список дисплей менеджеров которые могут пустить в read-only снапшот созданный snapper

- LightDM - пустой чёрный экран или черный экран с мерцающим курсором

  2 Решения:

- 1. Отредактировать конфиг ``/etc/lightdm/lightdm.conf``, раскомментировать и изменить значение на true

```conf
[LightDM]
...
user-authority-in-system-dir=true
```

- 2. [Не работает] Необходимо добавить subvolume

```txt
/var/lib/AccountsService
```

- SDDM - Работает, заходит в снапшот без проблем и каких-либо настроек.\
Только вылезает предупреждающее окно sddm, которое можно проигнорировать

```txt
Configuration file "/var/lib/sddm/.config/sddm-greeterrc" not writable
Please contact your system administrator
```

[Не проверено] Есть решение взятое [отсюда](https://github.com/bach001/notes_on_install_LFS/blob/main/fix_su_login_unlock_failed)\
Вводим данную команду

```sh
chown -R sddm:sddm /var/lib/sddm/.config
```

Следующее что хочется заметить это то что если править конфиг sddm по пути `/usr/lib/sddm/sddm.conf.d/default.conf`\
И выставить экспериментальную опцию wayland как показано ниже

```conf
[General]
...
DisplayServer=wayland
```

Тогда в момент когда sddm должен появится происходит бесконечный чёрный экран с нижнем подчёркиванием без мерцания\
Такое у меня происходит на sway с sddm (виртуалка QEMU/KVM с virt-manager)

Решения я пока не знаю, кроме как:

1. Не менять значение DisplayServer на wayland
2. Не использовать SDDM и запускать из под TTY используя правив ``~/.zprofile``

- LXDM - Тоже работает, заходит в снапшот без проблем и каких-либо настроек
- GDM - Доходит до [ OK ] Reached target Graphical interface и застревает\
  Решение: необходимо добавить subvolume

```txt
/var/lib/AccountsService
/var/lib/gdm
```

Они содержат информацию о вошедшем в систему пользователе, а также информацию об отображении Gnome. Эти каталоги всегда должны быть доступны для записи. Таким образом, мы, к сожалению, вынуждены разделить их.

Необходимо сделать эти субтома **top level 5**. Причина по которой мы это делаем это возможность монтировать данный субтом, без этого у меня не получалось монтировать и добавлять запись в fstab

### Создание подтомов (subvolume) находясь в основной системе

Для начало необходимо узнать где находится toplevel 5 подтом в котором можно находятся другие вложенные подтома

Узнать это можно несколькими командами

```sh
# Первая
sudo btrfs su l /
# Вторая
mount | column -t
```

Переходим в каталог/раздел где лежат все подтома и воздаём те что необходимы

```sh
cd /
sudo btrfs su cr @AccountsService
sudo btrfs su cr @gdm
```

Смотрим что получилось командой

```txt
# btrfs su l /
ID 275 gen 109 top level 5 path @AccountsService
ID 276 gen 121 top level 5 path @gdm
```

Создаём каталоги для монтирования

```sh
sudo mkdir -v /var/lib/AccountsService
sudo mkdir -v /var/lib/gdm
```

Для проверки мы можем монтировать данные субтома

```sh
sudo mount -v /dev/vda2 /var/lib/AccountsService -o subvol=@AccountsService
```
НО вместо обычного монтирования лучше создать запись в **fstab**

```conf
# /dev/vda2 LABEL=ArchLinux
UUID=c41b358c-2a57-412c-b710-eda6d62b1591       /var/lib/AccountsService        btrfs           rw,noatime,compress=zstd:2,discard=async,space_cache=v2,subvolid=275,subvol=/@AccountsService      0 0

# /dev/vda2 LABEL=ArchLinux
UUID=c41b358c-2a57-412c-b710-eda6d62b1591       /var/lib/gdm    btrfs           rw,noatime,compress=zstd:2,discard=async,space_cache=v2,subvolid=276,subvol=/@gdm       0 0
```

И монтируем

```sh
sudo systemctl daemon-reload
sudo mount -a
```

При установке окружения Gnome пакет **accountsservice** и **gdm** жаловались на права доступа

```txt
...
установка accountsservice
предупреждение: права доступа различаются у каталога /var/lib/AccountsService/
файловая система: 755  пакет: 775
установка gdm
предупреждение: права доступа различаются у каталога /var/lib/gdm/
файловая система: 755  пакет: 1770
...
```

Изменил как им требуется

```sh
sudo chmod 775 /var/lib/AccountsService/
sudo chmod 1770 /var/lib/gdm/
```

## Итоги и проблемы

> После проделанных операций, окружением гном и его дисплей менеджер gdm позволяет загрузится в снимок\
> НО у меня не получается сделать откат на снимок используя snapper-rollback который соответствует разметке Arch Linux.\
> Вылезает ошибка что файловая система только для чтения /btrfsroot.\
> Что-то мешает **snapper-rollback** выполнение отката даже с правами sudo (плюс в конфиге **/etc/snapper-rollback.conf** выставлено dev устройство).\
> Ранее на других окружениях с другими DM это срабатывало.

Решение:

Необходимо в Arch ISO под chroot'ом создать папку /btrfsroot

```sh
mkdir /btrfsroot
```

И смонтировать её с данными флагами

ОБЯЗАТЕЛЬНО:

1. Указываете тот номер раздела который у вас смонтирован как / у меня это ``/dev/vda2``
2. В `subvolid` указываете 5

```sh
mount -v -o noatime,compress=zstd:2,space_cache=v2,subvolid=5 /dev/vda2 /btrfsroot
```

После чего команда `sudo snapper-rollback [num_snapshots]` работает и откатывает систему

После отката в выхлопе команды `sudo btrfs su li /` появился подтом с таким именем

```txt
ID 256 gen 7 top level 5 path @2023-08-29T01:33
```

Этот подтом находится по пути /btrfsroot/\
В **@2023-08-30T16:08** находится сломанная система с удалённым которую мы откатили на рабочий снимок

Удалить его можно командой

```sh
sudo btrfs su delete /btrfsroot/@2023-08-29T01:33
```

### Отсутствующие метки новых снимков в grub

Иногда после выполнения отката используя команды `snapper-rollback` могут не показываться созданные метки снимков в меню grub которые делает grub-btrfs

Исправляется это переустановкой grub, выполнением grub-install в тоже самое место и grub-mkconfig (если у вас есть хук `grub-upgrade.hook` тогда он сам за вас всё сделает)

И он будет удалён из списка

## Что использовать systemd сервис grub-btrfsd или snap-pac-grub и в чём разница?

> Разница заключается в том что сервис grub-btrfsd обновляет записи в фоне при любом изменении или добавлении снимков в /.snapshots\
> а пакет [snap-pac-grub](https://aur.archlinux.org/packages/snap-pac-grub) добавляет записи только для pre и post транзакциях pacman и обновляет записи сразу (однако крайний post не включает)

Включается сервис grub-btrfsd командой

```bash
systemctl enable grub-btrfsd.service
```

## Настройка snapper для снимков /home каталога

Здесь подготовка и настройка практически такая же как и с корневым каталогом / но с мелкими отличиями\
Однако снимки и их восстановление и откат будет происходить по другому

Создаём подтом с названием ``@home.snapshots`` нужно чтобы он был также toplevel

```sh
sudo btrfs su cr @home.snapshots
sudo mkdir /home/.snapshots
```

```conf
# /dev/vda2 LABEL=ArchLinux
UUID=870f5b9d-1b03-4251-b3aa-780df552a5ec  /home/.snapshots  btrfs       rw,noatime,compress=zstd:2,discard=async,space_cache=v2,subvolid=300,subvol=/@home.snapshots  0 0
```

Перезагружаем демонов и монтируем

```sh
sudo systemctl daemon-reload
sudo mount -va
```

Размонтируем и удаляем /home/.snapshots

```sh
umount -v /home/.snapshots
rm -rfv /home/.snapshots
```

Создаю конфигурацию Snapper для /home\
Snapper отслеживая этот путь будет создавать снимки всех пользователей (если они присутствуют)

```sh
snapper -c home create-config /home
```

Удаляем подтом /home/.snapshots Snapper'а

```sh
btrfs subvolume delete /.snapshots /home/.snapshots
```

Пересоздаём и переподключаем /home/.snapshots

```sh
mkdir -v /home/.snapshots
mount -v -a
```

Меняем права доступа для легкой замены снимка @ в любое время без потери снимков snapper.

```sh
chmod -v 750 /home/.snapshots
```

Доступ к снимкам для non-root пользователям

```sh
chown -vR :wheel /home/.snapshots
```

Настройка Snapper

Не создавать timeline-снимки для ``/home``

```sh
sed -i "s|^TIMELINE_CREATE=.*|TIMELINE_CREATE=\"no\"|g" /etc/snapper/configs/home
```

ОБЯЗАТЕЛЬНО добавьте в конфиг файле ``/etc/updatedb.conf`` внутри **PRUNEPATHS** путь снимков ``/home/.snapshots`` от предотвращения индексирования программой "updatedb" т.к это будет замедлять работу системы

Готово, теперь как вернуть home до момента чистой установки (TTY) [делал после отката root]\

Смотрим список снимков home командой

```sh
# 0 - это текущее состояние системы, 1 - это созданный снимок свежей установки
snapper -c home ls
```

И откатываем командой и на выходе получится

```sh
snapper -c home undochange 1..0
# Выхлоп:
создать:0 изменить:1 удалить:21322
```

Работает прекрасно, теперь $HOME чист от KDE конфиг файлов

### Исключить каталоги от снимков в $HOME

Для этого надо просто добавить подтом (subvolume) как путь, НО перед этим, каталоги с этим путём надо создать

В качестве примера я буду использовать flatpak виртуальную машину gnome-boxes

```sh
mkdir -p ~/.var/app/org.gnome.Boxes/data/gnome-boxes/
```

Создаём подтом images командой от пользователя (non-root)

```sh
btrfs su cr ~/.var/app/org.gnome.Boxes/data/gnome-boxes/images
```

Теперь образы (qcow) которых не будут присутствовать в снимках и собственно не будут занимать место и раздувать его

Также я дополнительно добавил ``~/.local`` и ``~/.cache``, так как они уже содержат файлы мне необходимо их переместить во временную папку, потом сразу создать подтом и обратно переместить из временной папки в ``~/.local`` и ``~/.cache``

```sh
mkdir -v ~/temp_local
mkdir -v ~/temp_cache
mv -v ~/.local/* ~/temp_local
mv -v ~/.cache/* ~/temp_cache
btrfs su cr ~/.local
btrfs su cr ~/.cache
mv -v ~/temp_local/* ~/.local/
mv -v ~/temp_cache/* ~/.cache/
rm -rv ~/temp_cache/ ~/temp_local/
```

Смотрим как это выглядит

```txt
# btrfs su li /
ID 370 gen 937 top level 257 path @home/user/.var/app/org.gnome.Boxes/data/gnome-boxes/images
ID 371 gen 941 top level 257 path @home/user/.local
ID 372 gen 941 top level 257 path @home/user/.cache
```

Теперь снапшотим $HOME используя snapper команду

```sh
snapper -c home create
```

И после обзора этого снимка видно что файлов внутри ``~/.local`` и ``~/.cache`` нету

## Советы и трюки

Ручные одиночные снимки

```sh
snapper -c root create -c number
# или
snapper -c root create -c timeline
```

(Не проверено) Сравнить снапшот файл

```sh
# Пример
snapper -c root diff id..0 filename
```

Восстановление удаленного файла

```sh
# Пример
sudo snapper -c root undochange 1152..0 /etc/X11/xorg.conf
```

(Не проверено) Восстановить предыдущую версию файла из снимка

```sh
# Примеры:
# Вернуть состояние файла из post(5) в pre(2)
sudo snapper -v undochange 2..5 /etc/sysconfig/apache2
#sudo snapper -c root -v undochange id..0 FILENAME1 FILENAME2
```

Удалить снимок

> Примечание: При удалении pre-снимка всегда следует удалять соответствующий ему post-снимок, и наоборот.

Чтобы удалить снимок с номером N, выполните:

```sh
snapper -c config delete N
# Примеры
snapper -c root delete 65 70
snapper -c root delete 65-70
# Чтобы немедленно освободить пространство, используемое снимками, используйте --sync:
snapper -c root delete --sync 65
```

Как узнать занимаемое место снимков?

- [Видео инструкция другой метод](https://www.youtube.com/watch?v=KahWNBx9TG8)

```sh
# Для отдельного снимка укажите номер
sudo btrfs filesystem du -s /.snapshots/N
# Для всех снимков (долго)
sudo btrfs filesystem du -s /.snapshots/*
```

Покажу на примере моего древнего снимка 2737 которому 7 месяцев и он всё это время не удалялся,\
в то время сейчас у меня текущий по дате снимок является 6402.

```txt
     Total   Exclusive  Set shared  Filename
  20.88GiB    18.66GiB     1.86GiB  /.snapshots/2737
```

При удалении этого снимка он освободил у меня около 10-11гб.\
Поэтому следует обращать внимание на **Exclusive**

## Как откатится в read-write снапшот после сломанной системы (Arch way)

> [Как указано из Arch Wiki](https://wiki.archlinux.org/title/Snapper#Suggested_filesystem_layout) команда `snapper rollback` [не предназначена для текущей разметки Arch Linux](https://github.com/jrabinow/snapper-rollback/issues/16#issuecomment-1679407121), когда я тестировал её, после выполнения ранее удалённый рекурсивно /etc не восстановила и из-за этого я не смог загрузится в систему. Поэтому необходимо использовать другой метод для отката системы.

Для самого простого и не затруднительного метода есть AUR пакет [snapper-rollback](https://aur.archlinux.org/packages/snapper-rollback/) который соответствует схеме разметки Arch Linux

При его установке необходимо отредактировать конфиг `/etc/snapper-rollback.conf` и в самом конце раскоментировать строку **dev** и указать ваш **/dev/sda[номер]** раздел на который смонтирован как /

Если у вас есть записи снапшотов grub при установке grub-btrfs и в вашей системе что-то сломалось, загружаемся в рабочий снимок и для отката вводим команду

```sh
sudo snapper-rollback [номер снапшота из snapper ls]
```

И после перезагрузки у вас будет восстановленная система

### Не получается откатится из-за малого объёма диска

Если например случилась проблема при обновлении Arch Linux и у вас нету места в разделе / тогда команда `sudo snapper-rollback` не сможет сделать откат выдав ошибку о том что недостаточно места

```txt
...
OSError: [Errno 28] No space left on device: '/btrfsroot/@' -> '/btrfsroot/@2023-11-03T19:25'
```

Именно поэтому **не рекомендуется** не заполнять в край / раздел если пользуетесь файловой системой Btrfs

Однако если боитесь столкнутся с таким попробуйте вот что рекомендуется делать:

1. Удалить старые снимки

- Воспользовавшись snapper

```sh
# Смотрим id каких ненужных снимков необходимо удалить
snapper -c root ls # Или `snapper ls`
# Удаляем снимок с немедленным очищением пространства
snapper delete --sync [id]
```

- Можно это сделать ручным методом

```sh
sudo btrfs subvolume delete /.snapshots/№/snapshot
```

2. Ограничить кол-во снимков

```sh
snapper -c root set-config "NUMBER_LIMIT=2"
snapper -c root set-config "NUMBER_LIMIT_IMPORTANT=2"
```

### Можно обойтись без snapper-rollback от сделать это вручную?

Ответ - да, можно. Вот как это сделать находясь в выбранный через меню grub read-only снимок:

```sh
# Переходим в root оболочку
sudo su

# Откидываем сломанный текущий подтом
mv /btrfsroot/@ /btrfsroot/@$(date +"%Y-%m-%dT%H:%M")

# Теперь заменяем сломанный на тот номер снимка на котором находитесь
btrfs subvolume snapshot /btrfsroot/@snapshots/<номер>/snapshot /btrfsroot/@

# Делаем его основным для загрузки
btrfs subvolume set-default /btrfsroot/@
```

Готово

### Проблема: Не добавляются новые записи снимков в grub при использовании grub-btrfsd

Я сталкивался с этим после отката в окружении Gnome на VM когда пытался удалить /etc директорию.\
После отката новые снимки почему-то не добавлялись в grub меню

Решением было переустановка пакета grub с командой `grub install ...` и дальнейшим пересозданием конфига `grub-mkconfig ...`
