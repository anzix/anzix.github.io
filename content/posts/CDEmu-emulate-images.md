+++
title = "Эмуляция оптических дисков используя CDEmu (альтернатива Daemon-Tools)"
date = 2023-03-03T21:27:49+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

Источники:

- [Arch Wiki CDemu](https://wiki.archlinux.org/title/CDemu)

CDEmu может монтировать .nrg .iso .cue и другие форматы

Качаем пакет

```bash
sudo pacman -S cdemu-client
```

> Примечание: если у вас обычное ядро Linux, устанавливаем `vhba-module-dkms`

```bash
sudo pacman -S vhba-module-dkms
```

Для активации демона который будет удобно монтировать наши образы вставляем в .xprofile (для DM) или .xinitrc (запуск с TTY)

```bash
cdemu-daemon &
```

Для тех кто использует дисплей менеджеры

```bash
systemctl --user enable --now cdemu-daemon.service
```

## Монтирование через проводник

В проводнике pcmanfm при нажатии на iso файл правой кнопкой мыши появится действие "Клиент CDEmu", жмём на него и диск смонтирован

![image](/images/CDEmu-emulate-images/1666680474.png)

Появится уведомление что диск был смонтирован и его можно открыть

![image](/images/CDEmu-emulate-images/1666681885.png)

![image](/images/CDEmu-emulate-images/1666677451.png)

Далее уже можно юзать wine для установки игры, в общем всё стандартно

```bash
WINEPREFIX="$HOME/.local/share/wineprefixes/TRA" wine /run/media/[username]/Tomb_Raider_AnniVERSARY/setup.exe
```

Через терминал можно вызвать status команду какие эмулированные диски смонтированы

```bash
cdemu status
```

После того как установили игру, смонтированный образ можно удалить командой

```bash
cdemu unload 0
```

## Монтирование более 1 эмулируемых образов в CDEmu

Была проблема когда примонтированным дисководом .mds образ содержал в себе ещё .mds образы которые я не смог смонтировать CDEmu. При нажатии "Клиент CDEmu" ничего не происходило, а в терминале при вводе команды возникала ошибка

```sh
cdemu load 1 /run/media/[username]/NFS_MF_BE/CD1/NFSMW_DISC1_HKZonda_by_P2PZone.org.mds
ОШИБКА: Не удалось смонтировать образ: g-io-error-quark: GDBus.Error:net.sf.cdemu.CDEmuDaemon.errorDaemon.InvalidArgument: Некорректный номер накопителя! (36)
```

Проблема была в не актуальной [Arch Wiki CDEmu](https://wiki.archlinux.org/title/CDemu) статье где не было указано о добавлении эмулируемого sr устройство
Нашёл я на странице мануала необходимую мне опцию

```bash
man cdemu
```

- `add-device` - Создает еще одно виртуальное устройство
  Для добавления доп-го эмулируемого sr устройства выполняем данную команду

```bash
cdemu add-device
```

```sh
Накопитель добавлен успешно.
```

Теперь наконец-то удалось смонтировать второй образ диска обеими вариантами через терминал (командой cdemu load 1) или нажав на образ в проводнике "Клиент CDEmu"

Если создали ненужное пустое sr устр-во, его можно удалить данной командой

```bash
cdemu remove-device
```

```sh
Накопитель удален успешно.
```
