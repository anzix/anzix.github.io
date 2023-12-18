+++
title = "!!Виртуальная машина Qemu KVM Virt-manager!!"
date = 2023-08-13T13:00:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

- [CLI Видеоинструкция (gentoo edition)](https://www.youtube.com/watch?v=9trs2SNeW3o&list=WL&index=7)
- [Вики libvirt](https://wiki.libvirt.org/page/Main_Page)
- [Вики kvm](https://www.linux-kvm.org/page/HOWTO)

Требования:

- У вас должен быть установлен и настроен polkit аутентификатор

- Требуется включить в Bios'е (для процессоров Intel) технологию виртуализации VT-d\
  У меня на HuananZhi X79 ZD3 она находится во вкладке **Chipset** - **North Bridge** - **IOH Configuration** - **Intel VT for Directed I/O Configuration** и включаем **Intel VT-d**

![image](/images/qemu-gpu-passthrough-and-etc/bios_vt-d.png)

- Требуется отключить CSM в Bios'е\
  Находится во вкладке **boot** - **CSM parameters** - меняем **Launch CSM** на disabled

![image](/images/qemu-gpu-passthrough-and-etc/bios_csm.png)

Устанавливаем все эти пакеты

```sh
sudo pacman -S virt-manager qemu qemu-arch-extra dnsmasq iptables-nft dmidecode edk2-ovmf swtpm
```

Соглашаемся на удаление iptables

## Настройка KVM и Libvirt

Раскомментируем эти строки в `/etc/libvirt/libvirtd.conf`

```txt
unix_sock_group = "libvirt"
unix_sock_rw_perms = "0770"
```

Или вводим в терминале

```sh
sudo sed -i 's/^#unix_sock_group/unix_sock_group/' /etc/libvirt/libvirtd.conf
sudo sed -i 's/^#unix_sock_rw_perms/unix_sock_rw_perms/' /etc/libvirt/libvirtd.conf
```

Добавляем нашего юзера в приведённые ниже группы

```sh
sudo usermod -aG libvirt,kvm,input $(whoami)
```

Вот инфо про данные группы

- kvm - Доступ к виртуальным машинам использующие [KVM](https://wiki.archlinux.org/title/KVM)
- libvirt - Позволяет обычному пользователю (через PolicyKit) подключиться к демону libvirtd, не запрашивая пароль root
- input - необходим для проброса evdev устройств usb мышки, клавиатуры и т.д

Просмотреть группы принадлежащие юзеру командой `groups $(whoami)`

Если у вас образы iso в другом разделе своего диска, тогда вам потребуется отредактировать `qemu.conf`, и дать привилегии qemu читать данный iso.

> Без этого у вас [вылезет данная ошибка](/posts/qemu-gpu-passthrough-and-etc/#1-problema-nevozmozhnost-zapustit-vm)

`sudo nvim /etc/libvirt/qemu.conf`

```txt
user = "libvirt-qemu" -> user = "имяпользователя"
group = "libvirt-qemu" -> group = "wheel"
```

Для упрощения можете ввести эти команды в терминале

```sh
sudo sed -i "s|^#user = .*|user = \"${USERNAME}\"|g" /etc/libvirt/qemu.conf
sudo sed -i "s|^#group = .*|group = \"wheel\"|g" /etc/libvirt/qemu.conf
```

В параметрах ядра необходимо прописать опции `intel_iommu=on iommu=pt` и ребутнутся

> Пояснение: Делается это для того чтобы можно было пробросить свою видеокарту в VM

Запускаем сервис

```sh
sudo systemctl enable --now libvirtd
```

Для проверки сервиса

```sh
systemctl status libvirtd
```

[В статусе были ошибки от dnsmasq которые я исправил](/posts/qemu-gpu-passthrough-and-etc/#bad-name-at-etc-hosts-line)

### Настройка вирт. сети через GUI Virt Manager

Проводим все манипуляции через через GUI Virt Manager'а

Запускаем GUI “Менеджер виртуальных машин” через меню приложений (rofi или dmenu)\
Или командой `virt-manager` терминале

Выбираем созданную ВМ из списка и жмём "Свойства подключения" который находятся в тулбаре менеджера "Правка"

В открывшимся окне во второй вкладке "Вирт-ые сети" можно

Создать новую вирт. сеть\
Вкл/выкл вирт. сеть \
Удалить вирт. сеть

Название сети (которое можно поменять), состояние и её автозапуск

Включаем вирт. сеть и ставим галку о автозапуске

Вуаля, теперь можно создать виртуальную машину

### Через терминал

Список всех вирт. сетей

```txt
$ sudo virsh net-list --all

 Имя       Состояние    Автозапуск   Постоянный
-------------------------------------------------
 default   не активен   no           yes
```

Включить [default] вирт. сеть

```sh
sudo virsh net-start default
```

Автозапуск вирт. сети [default] при запуске системы

```sh
sudo virsh net-autostart default
```

Готово

#### bad name at /etc/hosts line **\***

[Сурс](https://github.com/StevenBlack/hosts/issues/333)

С прописанными в /etc/hosts от [StevenBlack](https://github.com/StevenBlack/hosts) из [данного моего гайда](/posts/hosts-file-by-stevenblack), dnsmasq возмущается на данные строки

```txt
$ systemctl status libvirtd
...
dnsmasq[54577]: bad address at /etc/hosts line 25
dnsmasq[54577]: bad address at /etc/hosts line 40796
dnsmasq[54577]: bad address at /etc/hosts line 40797
dnsmasq[54577]: bad address at /etc/hosts line 40798
dnsmasq[54577]: bad address at /etc/hosts line 72163
dnsmasq[54577]: bad address at /etc/hosts line 72164
dnsmasq[54577]: bad address at /etc/hosts line 72165
```

Решил это просто отредактировав файл hosts закомментировав (НЕ удалять) эти указанные номером строки

Чтобы быстро перейти к данной строчке вот небольшой лайфках\
В vim/nvim вводим `:'номерстроки'` и жмём enter

```txt
fe80::1%lo0 localhost
0.0.0.0 27--01bbcpolice.powercoremedia.com
0.0.0.0 27--m01police.55fifayellow.com
0.0.0.0 27--m01police.best-rc-store.com
0.0.0.0 www.27--01bbcpolice.powercoremedia.com
0.0.0.0 www.27--m01police.55fifayellow.com
0.0.0.0 www.27--m01police.best-rc-store.com
```

Или воспользуйтесь данной командой для автоматического удаления данных строк

```sh
sudo sed -i '/fe80::1%lo0 localhost/d;/0.0.0.0 27--01bbcpolice.powercoremedia.com/d;/0.0.0.0 www.27--01bbcpolice.powercoremedia.com/d' /etc/hosts
```

И рестарт libvirtd

```sh
sudo systemctl restart libvirtd
```

Нажал Начать Установку
Первое с чем я столкнулся и то как это решил

Автоматически запустил live CD Arch Linux
SSH соединение в VM произошёл и всё прекрасно работает (соединение default NAT)
Только вот на андроид через Termux не работает

На Manjaro Plasma Live Iso интерфейс лагает
Решил эту проблему довольно просто

## !Проблема: Невозможность запустить VM!

Столкнулся с данной ошибкой, из-за того что место в котором расположено iso находится на другом разделе

```txt
Не удалось завершить установку:
«внутренняя ошибка: QEMU неожиданно завершил работу монитора: 2022-07-20T16:53:24.729056Z
qemu-system-x86_64: -device {"driver":"virtio-blk-pci","bus":"pci.4","addr":"0x0","drive":"libvirt-1-format","id":"virtio-disk0","bootindex":1}:
Could not open '/media/Distrib/iso/Linux/archlinux-2022.07.01-x86_64.iso': Permission denied»
```

Решилось путём редактирования конфига `/etc/libvirt/qemu.conf`. Раскоментируем строки для предоставления qemu все полномочия чтения данного iso\
[Сурс](https://github.com/jedi4ever/veewee/issues/996#issuecomment-59161407)

```txt
user = "libvirt-qemu" -> user = "имяпользователя"
group = "libvirt-qemu" -> group = "wheel"
```

Или ввести эти команды в терминале

```sh
sudo sed -i "s|^#user = .*|user = \"${USERNAME}\"|g" /etc/libvirt/qemu.conf
sudo sed -i "s|^#group = .*|group = \"wheel\"|g" /etc/libvirt/qemu.conf
```

Сохраняем конфиг и перезагружаем сервис libvirtd

```sh
sudo systemctl restart libvirtd
```

## !Проблема: Termux соединение к VM!

На Андроид по SSH не коннектится в VM с помощью программы Termux\
Когда жму коннект мне не выдаёт промпт о вводе пароля

Пока не решил данную проблему

## !Проблема: увеличенный текст в оконном менеджере!

В Arch Linux при настроенным тайловым оконным менеджере всё стало увеличенным включая терминал

Происходит это когда я юзаю дисплей Virtio + OpenGl ускорением

При создании ВМ с Arch Linux и любом окружении WM/DE скейлит текст в терминале alacritty (в конфиге alacritty размер шрифта выставлен 14)

Текст выглядит больше обычного

## Проблема: Использовании picom в VM

В настройках виртуального оборудования во вкладке Видео выставлено Модель: Virtio с включенным 3D ускорением\
При использовании моего picom config'а как композитора в окружениях таких как LXDE, LXQT и т.п перемещение окон идёт с отставанием с заметным input лагом

Решение: В конфиге picom выставить

```conf
backend = "xrender"; -> "glx"
vsync = true; -> false
```

И запустить picom командой

```sh
picom -b --experimental-backends &
```

Теперь всё очень плавное на 100% при 60hz дисплей в гостевой вм

## Проблема: Не могу создать снапшот активное состояние гостевой машины

Ошибка создания снимка: Операция не поддерживается: внутренние снимки виртуальной машины с микропрограммой на основе pflash не поддерживаются

```txt
Error creating snapshot: Operation not supported:
Internal snapshots of a VM with pflash based firmware are not supported.
```

- Решение для гостевой Linux машине: создавать снимок гостя можно только в выключенном состоянии (UEFI)

- Решение для гостевой Win10 машине: Смог исправить это отредактировав xml файл в данной строке вместо **type="pflash"** написать **type="rom"**

```xml
<loader readonly="yes" type="rom">/usr/share/edk2-ovmf/x64/OVMF_CODE.fd</loader>
```

После я смог сделать снимок экрана

## Проблема: Нету bootloader'а grub после импорта qcow образа

[Сурс](https://superuser.com/questions/1235970/stuck-on-uefi-interactive-shell-with-mac-os-x-high-sierra-vm)

После переустановки Арча на хосте я импортировал qcow образ и обнаружил что на нём вместо появления grub и запуска я получаю UEFI Interactive Shell

![image](/images/qemu-gpu-passthrough-and-etc/uefi-interactive-shell.png)

Решаение

```txt
Выходим из uefi shell введя exit
Далее в bios'е выбираем "Boot Maintenance Manager" - "Boot From File" - Выбрать "EFI, PciRoot..." - мы находимся в boot разделе
Выбираем "<EFI> - <arch> - <grubx64.efi>" после чего появится grub и загрузится в гостевую машину
```

Чтобы при последующей перезагрузке не сталкиватся с этим необходимо всего-лишь заново установить grub в `/boot/efi` и обновить конфиг grub

```sh
sudo grub-install --efi-directory=/boot/efi
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## !Проблема: Не могу удалить Дисплей Spice!

Хотел проброс видюхи на win10 но появилась такая проблема

```txt
Ошибка удаления устройства: конфигурация не поддерживается: chardev 'spicevmc' not supported without spice graphics
```

[Временное решение](https://superuser.com/a/1726093)

Добавить удалённый канал spice\
Поставить Видео - none

## !Проблема: ``<qemu:commandline>`` не вставляется в xml файл virt-manager!

- [Источник](https://unix.stackexchange.com/questions/495703/qemu-commandline-arguments-to-lib-virt-are-not-accepted-unable-to-save-xml-fil)

**Решением** было сразу при редактировании вставить заполненную строку с содержимым т.е где троеточие должны быть вставлены переменные

```xml
<qemu:commandline>
...
</qemu:commandline>
```

Дополнительно вставив в самый вверх

```xml
<domain xmlns:qemu="http://libvirt.org/schemas/domain/qemu/1.0" type="kvm">
```

И после применения у меня данные строки сохранились

## Общий буфер обмена

Проверено на: VM Arch Linux с окружением KDE Plasma, Gnome, Xfce4

Буфер обмена работает как с гостя на хост так и наоборот

Гостю необходимо установить пакет

```sh
sudo pacman -S spice-vdagent
```

**Убедитесь** чтобы в virt-manager'е было добавлено оборудование - Канал Spice с типом уст-ва Агент SPICE (spicevmc)\
Если оборудование у вас есть тогда просто перезагружаем гостевую машину, после скачивания пакета

Для пользователей системы инициализации OpenRC (Artix Linux) устанавливаем пакеты

```sh
sudo pacman -S qemu-guest-agent spice-vdagent spice-vdagent-openrc
```

Запускаем сервис

```sh
rc-update add spice-vdagent
```

### Как на Window Manager'е задействовать общий буфер обмена

[Сурс решения](https://unix.stackexchange.com/a/642568)

Столкнулся с подобным я на [icewm](https://wiki.archlinux.org/title/IceWM).\
Покопавшись нашёл решение, необходимо было запустить команду ``spice-vdagent`` в терминале, можно также её добавить в автозапуск файла ``~/.xprofile`` (Для DM) или ``~/.xinitrc`` (запуск с TTY)

Или также можно стартануть systemd сервис

```sh
sudo systemctl start spice-vdagentd
```

## !Общие папки (Shared Folders) используя virtiofs!

[Инструкция на англ](https://sysguides.com/share-files-between-kvm-host-and-linux-guest-using-virtiofs/)

На хосте выбираем место где будет гость цеплять наши файлы

```sh
mkdir ~/Shared
```

В VirtManager добавляем оборудование "Файловая система" с данными настройками\
Драйвер: virtiofs\
Путь на хосте: /home/$USER/Shared (т.е то место где гость будет цеплять файлы)\
Путь в гостевой ОС: Qemu (место в гостевой VM используемое для идентификации общего каталога, который необходимо смонтировать в гостевой ОС)

После добавления уст-ва загружаемся в VM и создаём каталог для монтирования общей папки

```sh
mkdir ~/Qemu
```

Монтируем

```sh
# Пример
sudo mount -t virtiofs [Имя папки хоста которую ходим передать] [Путь созданного каталога в гостевой ОС для монтирования]
# Команда
sudo mount -t virtiofs Qemu /home/$USER/Shared
```

И вуаля Общая Папка появилась с файлом который мы будучи хостом поместили внутрь\
Чтобы сделать монтирование Shared Folders постоянным при включении гостя, прописываем в ``/etc/fstab``

```conf
Shared		/home/onix/Qemu	virtiofs	defaults	0 0
```

Вот и всё!

## Общие папки (Shared Folders) используя virtio-9p

[Инструкция ENG](https://www.baeldung.com/linux/qemu-from-terminal#6-sharing-a-directory-between-host-and-guest)

На хосте выбираем место где будет гость цеплять наши файлы

```sh
mkdir ~/Shared
```

В VirtManager добавляем оборудование "Файловая система" с данными настройками\
Драйвер: virtio-9p\
Путь на хосте: /home/user/Shared (т.е то место где мы добавляем файлы с которыми хотим поделится чтобы гость их видел)\
Путь в гостевой ОС: host0 (идентификация общего каталога т.е mount tag, называть можно как угодно)

После добавления уст-ва загружаемся в VM и создаём каталог для монтирования общей папки

Папка будет называться vmshare и будет находится в домашнем каталоге гостя

```sh
mkdir ~/vmshare
```

Монтируем

```sh
# Пример
sudo mount -v -t 9p -o trans=virtio,version=9p2000.L [Индентификатор общего каталога т.е mount tag] [Путь созданного каталога в гостевой ОС для монтирования]
# Команда
sudo mount -v -t 9p -o trans=virtio,version=9p2000.L host0 ~/vmshare
```

Готово! Общая папка создана

## !!Резервирование образа qcow2 из /var/lib/libvirt/images/!!

- [Creating a Virtual Machine XML Dump](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/virtualization_deployment_and_administration_guide/sect-domain_commands-creating_a_virtual_machine_xml_dump_configuration_file)

Обычное копирование на мой NTFS HDD диск, производится успешно

```sh
sudo cp /var/lib/libvirt/images/win10.qcow2 /media/Other/backup_w10.qcow2
```

50gb передалось за 10 минут

Данной командой можно сдампить xml файл гостя

```sh
sudo virsh dumpxml win10 > ~/win10dump.xml
```

## Ручное редактирование xml файл

Есть два варианта

1. `sudo EDITOR=nvim virsh edit win10`
2. `sudo vim /etc/libvirt/qemu/win10.xml`

Если вы ДЕЙСТВИТЕЛЬНО не знаете что делаете, не делайте этого. Потому что virsh edit имеет проверка синтаксиса, чтобы выявлять некоторые проблемы. Совсем как visudo делает.

## Где находятся логи запуска Гостя VM?

В `/var/log/libvirt/qemu/{VMNAME}`

## Где находятся аргументы запуска qemu если запускать из Virt-Manager?

В `/var/log/libvirt/{VMNAME}.log`
