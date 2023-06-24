+++
title = "Snapper - бэкап файловой системы Btrfs"
date = 2023-03-02T12:59:48+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

Видео гайды:
[Первый](https://www.youtube.com/watch?v=Xynotc9BKe8&t=927s)
[Второй](https://www.youtube.com/watch?v=sm_fuBeaOqE)
[Третий](https://www.youtube.com/watch?v=_97JOyC1o2o)

```bash
yay -S snapper snap-pac grub-btrfs snp snapper-gui-git
```

- snapper - основной пакет
- snap-pac - делать снапшоты после каждого обновления пакетов Pacman
- grub-btrfs - добавляет grub меню снимков сделанных snapper'ом
- [snapper-support](https://aur.archlinux.org/packages/snapper-support) - Пакет поддержки для включения Snapper с поддержкой snap-pac и grub-btrfs
- [snp](https://aur.archlinux.org/packages/snp) - заворачивает любую shell команду в моментальном снимке snapper'а до выполнения команды (`snp sudo pacman -Syu`)
- [snapper-gui-git](https://aur.archlinux.org/packages/snapper-gui-git/) - Gui программа для создания снимков

Входим в root оболочку

```bash
sudo -s
```

Unmount .snapshots

```bash
umount /.snapshots
rm -rf /.snapshots
```

Create Snapper config

```bash
snapper -c root create-config /
```

**Примечание**: Если производите в режиме chroot используйте `--no-dbus` опцию
[Сурс](https://github.com/openSUSE/snapper/issues/537)

Delete Snapper's .snapshots subvolume

```bash
btrfs subvolume delete /.snapshots
```

Re-create and re-mount /.snapshots mount

```bash
mkdir /.snapshots
mount -a
```

Change default subvolume

```bash
sudo btrfs subvol lis /
sudo btrfs subvol get-def /
sudo btrfs subvol set-def 256 / # Make sure it is @
sudo btrfs subvol get-def /
```

Access for non-root users

```bash
chown :wheel /.snapshots
```

Создаём первоначальный снимок

```bash
snapper -c root create --description "Fresh Install"
```

# Configure Snapper

Позволять группе wheel использовать snapper ls non-root пользователям

```bash
sed -i "s|^ALLOW_GROUPS=.*|ALLOW_GROUPS=\"wheel\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_HOURLY=.*|TIMELINE_LIMIT_HOURLY=\"5\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_DAILY=.*|TIMELINE_LIMIT_DAILY=\"7\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_WEEKLY=.*|TIMELINE_LIMIT_WEEKLY=\"0\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_MONTHLY=.*|TIMELINE_LIMIT_MONTHLY=\"0\"|g" /etc/snapper/configs/root
sed -i "s|^TIMELINE_LIMIT_YEARLY=.*|TIMELINE_LIMIT_YEARLY=\"0\"|g" /etc/snapper/configs/root
```

Enable Snapper services

```bash
systemctl enable snapper-timeline.timer
systemctl enable snapper-cleanup.timer
```

Enable GRUB-BTRFS service

```bash
systemctl enable grub-btrfsd.service
```

Configure initramfs to boot into snapshots using overlayfs (read-only mode)
[Сурс](https://github.com/Antynea/grub-btrfs/blob/master/initramfs/readme.md)

```bash
sed -i "s|keymap)|keymap grub-btrfs-overlayfs)|g" /etc/mkinitcpio.conf
```

Пересоздаём initramfs

```bash
mkinitcpio -P
```

Пропускать снапшоты с updatedb (Предотвращает замедление моментальных снимков)

```bash
sudo sed -i 's/PRUNEPATHS = "/PRUNEPATHS = "\/.snapshots /' /etc/updatedb.conf
```

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

## (Не проверено) Как откатится в read-write снапшот после сломанной системы

В загрузчике grub выбираем снапшот и загружаемся в него. После загрузки он будет в режиме только чтение. Чтобы сделать откат в снапшот с чтением и записью вводим в терминал

```bash
snapper ls
sudo snapper --ambit classic rollback [номер снапшота из snapper ls]
```
