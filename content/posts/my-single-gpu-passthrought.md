+++
title = "!Проброс одиночной AMDGPU в виртуалку Win10!"
date = 2023-10-05T22:30:00+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux"]
+++

1. [URL](https://www.youtube.com/watch?v=hQL5h5GteTY&t=1327s) RUS полу-гайд\
   Работает. Однако из-за неполноценности видео-гайда при выключении гостя шинды экран не возвращает обратно на хост (черный экран).\
   Также из-за того что ставил qcow2 образ на HDD всё очень медленное, поставив его на SSD дало значительный прирост.

2. [URL](https://www.youtube.com/watch?v=7zrRFX4UK_s) Для Gentoo
3. [URL](https://www.youtube.com/watch?v=BUSrdUoedTo&t=664s) Muta
4. [URL](https://www.youtube.com/watch?v=h7SG7ccjn-g) Muta
5. [URL](https://www.youtube.com/watch?v=CwEVj00SwYM) На французском (видео немного устарело) актуальный от него же [гайд тут](https://gitlab.com/Mageas/single-gup-passthrough)\
   Всё работает также с исправленным чёрным экраном. Данный гайд работает на все 100%
6. [URL](https://www.youtube.com/watch?v=3BxAaaRDEEw) На дистре Fedora

Другие полезные источники

- [Сабреддит Vfio](https://www.reddit.com/r/VFIO/) (ENG)
- [Дискорд сервер Vfio](https://discord.com/invite/f63cXwH) (ENG)

## Требования

- В настройках virt-manager выставляем этот чекбокс "Включить редактирование XML"
- Вам необходимо [скачать драйвера virtio для W10/W11](https://github.com/virtio-win/virtio-win-pkg-scripts) позже в процессе установки windows их необходимо будет установить для доступа к виртуальному диску и доступ в интернет\
\
Внутри Git репо выбираете **Stable virtio-win ISO** версию драйверов

### Создаём гостевую VM Windows 10

Память выставляем на ваше усмотрение, если будете играть в триплA (AAA) игры и на хосте 16gb тогда выставляете около 12-14gb, чтобы было с запасом для хоста чтобы предостеречь от зависания виртуальной машины

Процессоров дадим гостю 10 если проц 12 поточный, чтобы на хосте был запас

В шаге 4 вам предложат где будет хранится ВМ т.е qcow2 образ и каков будет её размер, скажу сразу что **не рекомендую** хранить образ qcow2 на HDD разделе. Возможно из-за того что у меня файловая система на HDD ntfs но на виртуалке я сталкивался медленным интерфейсом в целом, переход на SSD (т.е закинув на пул default в котором будет расположен по пути `/var/lib/libvirt/images`) значительно улучшило производительность и отзывчивость системы.

> **Примечание**: Дополнительно увеличить производительность можно поставив формат образа не qcow2 а raw\
Отличие в том что qcow2 динамически распределяет файлы т.е будет заполнятся и становится всё больше по мере поступления данных, он медленнее в чтении и записи но занимает меньше места.\
Raw этого нет т.е скорость в чтении и записи будет больше (заметно на примере перемещение файлов с одного устройство на другое) но будет сразу занимать столько места сколько вы ему назначите.\
Минус raw в том что вы не сможете делать снапшоты своей VM\
[Сравнение Raw vs Qcow2](https://techpiezo.com/tech-insights/raw-vs-qcow2-disk-images-in-qemu-kvm/)

- Название VM: "win10"
- В оборудование VM добавляем CD-ROM и выбираем скачанный ISO драйвер и отметив его в списке меню загрузок.
- Во вкладке Обзор - Микропрограмму ставим UEFI (или UEFI secboot для Windows 11)
- Во вкладке Процессоры выставляем галочку "Копировать конфигурацию ЦП хоста (host-passthrough)" и "Установить топологию ЦП вручную", сокеты меняем на 1, ядра 5, потоков 2
- Во вкладке Sata диск 1 выставляем Virtio для наилучшей производительности
- Во вкладке NIC выставляем модель устройства: Virtio для наилучшей производительности
- (Для Windows 11) Во вкладке TPM добавляем эмулируемый TPM версия 2.0

Начинаем обычную установку Windows

Как только дойдёте до разметки диска вам необходимо загрузить драйвер для отображения разметки диска и указываете:\
`CD-дисковод (E:) virtio-win-xxx > amd64 > win10`
Жмём запустить

Путь драйвера для показа диска разметки -\
Как появится в списке "**Red Hat  VirtIO SCSI controller**" жмём Далее

После установки выключаю гостя

#### !Настройка VM!

В списке оборудования Virt-Manager

- Video меняю на none

Из оборудования удаляю:

- Планшет
- Channel spice
- Sound ich*

В "**PCI-устройство**" добавляю устройства для проброса

Проброс видюхи вместе с аудиоинтерфейсом

- `0000:03:00:0 Advanced Micro Devices, Inc. [AMD/ATI] Ellesmere [Radeon RX 470/480/570/570X/580/580X/590]`
- `0000:03:00:1 Advanced Micro Devices, Inc. [AMD/ATI] Ellesmere HDMI Audio [Radeon RX 470/480 / 570/580/590]`

Звук с колонок

- `0000:00:1B:0 Intel Corporation C600/X79 series chipset High Definition Audio Controller`

USB 2/3 контроллер

- `0000:00:1D:0 Intel Corporation C600/X79 series chipset USB2 Enhanced Host Controller #1`
- `0000:00:1A:0 Intel Corporation C600/X79 series chipset USB2 Enhanced Host Controller #2`
- `0000:07:00:0 VIA Technologies, Inc. VL805/806 xHCI USB 3.0 Controller`

В разделе "**Хранилище**" ставим "Выбрать или создать дополнительное пространство данных"\
И пробрасываем по отдельности наши разделы

К примеру мой ntfs HDD

```txt
/dev/sdb1
/dev/sdb2
/dev/sdb3
/dev/sdb4
````

Тип шины выбираем Virtio для наилучшей производительности.

## !!libvirt hook helper!!

Libvirt Hook Helper будет заставлять вашу систему (хост машину) выключать-запускать модули драйверов видеокарты, сервисы и ваш дисплей менеджер перед включением вм гостя win10 так и после выключения.

```sh
sudo mkdir /etc/libvirt/hooks
sudo -e /etc/libvirt/hooks/qemu
```

Вставляем в qemu [этот libvirt hook helper](https://gitlab.com/Mageas/single-gup-passthrough#libvirt-hook-helper)

<!-- Ниже это другое но схожее -->
<!---->
<!-- ```sh -->
<!-- sudo mkdir /etc/libvirt/hooks -->
<!-- wget -qO- https://gitlab.com/risingprismtv/single-gpu-passthrough/-/raw/master/hooks/qemu | sudo tee --append /etc/libvirt/hooks/qemu >/dev/null -->
<!-- sudo chmod +x /etc/libvirt/hooks/qemu -->
<!---->
<!-- wget -qO- https://gitlab.com/risingprismtv/single-gpu-passthrough/-/raw/master/hooks/vfio-startup.sh | sudo tee --append /etc/libvirt/hooks/vfio-startup.sh >/dev/null -->
<!-- sudo chmod +x /etc/libvirt/hooks/vfio-startup.sh -->
<!-- sudo ln -s /etc/libvirt/hooks/vfio-startup.sh /bin/vfio-startup.sh -->
<!---->
<!-- wget -qO- https://gitlab.com/risingprismtv/single-gpu-passthrough/-/raw/master/hooks/vfio-teardown.sh | sudo tee --append /etc/libvirt/hooks/vfio-teardown.sh >/dev/null -->
<!-- sudo chmod +x /etc/libvirt/hooks/vfio-teardown.sh -->
<!-- sudo ln -s /etc/libvirt/hooks/vfio-teardown.sh /bin/vfio-teardown.sh -->
<!---->
<!-- wget -qO- https://gitlab.com/risingprismtv/single-gpu-passthrough/-/raw/master/systemd-no-sleep/libvirt-nosleep@.service | sudo tee --append /etc/systemd/system/libvirt-nosleep@.service >/dev/null -->
<!-- sudo chmod 644 -R /etc/systemd/system/libvirt-nosleep@.service -->
<!-- sudo chown root:root /etc/systemd/system/libvirt-nosleep@.service -->
<!-- ``` -->

![image](/images/my-single-gpu-passthrought/libvirt-hooks-qemu-config.png)

Делаем данный файл исполняемым `sudo chmod +x /etc/libvirt/hooks/qemu`

[Config Libvirt Hooks](https://gitlab.com/Mageas/single-gup-passthrough#config-libvirt-hooks)

```sh
sudo -e /etc/libvirt/hooks/kvm.conf
VIRSH_GPU_VIDEO=pci_0000_03_00_0
VIRSH_GPU_AUDIO=pci_0000_03_00_1
```

Start/Stop Libvirt Hooks

Вставляем данную переменную с добавлением вашего имя ВМ

```sh
KVM_NAME="win10"
```

Создаём скрипт запуска хука

```sh
sudo mkdir -p /etc/libvirt/hooks/qemu.d/${KVM_NAME}/prepare/begin
sudo -e /etc/libvirt/hooks/qemu.d/${KVM_NAME}/prepare/begin/start.sh
```

Содержимое файла `start.sh`

![image](/images/my-single-gpu-passthrought/libvirt-hooks-start-sh.png)

Тоже делаем данный файл исполняемым `sudo chmod +x start.sh`

Далее создаём скрипт остановку хука

```sh
sudo mkdir -p /etc/libvirt/hooks/qemu.d/${KVM_NAME}/release/end
sudo -e /etc/libvirt/hooks/qemu.d/${KVM_NAME}/release/end/stop.sh
```

Содержимое файла `stop.sh`

![image](/images/my-single-gpu-passthrought/libvirt-hooks-stop-sh.png)

Тоже делаем данный файл исполняемым `sudo chmod +x stop.sh`

> Установил lightdm lightdm-gtk-greeter для того чтобы работал при выключении гостя **вход** обратно в хост машину без бесконечного чёрного экрана с которым я сталкивался.

## Докачиваем драйвера Virtio для W10/11

В **Диспетчере устройств** есть отсутствующие драйвера такие как

- Ethernet Controller
- PCI Device
- PCI Simple Communication Controller

Выбираем любой кликаем на **Обновить драйвер** - **Поиск драйверов локально** - жмём **Обзор**

Добавляем оборудование CD-ROM в virt-manager'е выбирая iso [virtio-win-xxx](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso) т.е `E:\` и жмём Ок - Next ([Github страница](https://github.com/virtio-win/virtio-win-pkg-scripts))

![image](/images/my-single-gpu-passthrought/adding-drivers.png)

После загрузке первого драйвера, продолжаем и с последующими, повторяя тоже самое.

Когда закончили, следует скачать и установить дрова для проброшенной видюхи дабы иметь возможность изменить разрешение и герцовку моника.

## Включение Hyper-V

Используя PowerShell и ввести:

```PowerShell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

Включение Hyper-V через настроки:

Введите в поиске "Включение и отключение компонентов", выбрав **Hyper-V** и нажать **Ok**.



## !!Hyper-V Enlightenment и общие оптимизации xml файла VM!!

- [Все параметры hyper-v enlightenment](https://www.qemu.org/docs/master/system/i386/hyperv.html)
- ~~[VrChat (EAC) запуск на виртуальной машине](https://docs.vrchat.com/docs/using-vrchat-in-a-virtual-machine)~~

Что такое Hyper-V Enlightenment?

Если коротко то это оптимизация гостя ОС при включённом Hyper-v чтобы справляться с виртуальными задачами т.е мы оптимизируем определённые задачи чтобы улучшить их взаимодействие между хостом и гостём что может снизить накладные расходы тем самым повысив производительность.

*Enlightenment (просвещение) - так называемый "пара-виртуализированное расширение". Интерфейсы которые ядро Linux или QEMU предоставляют Windows гостю, предназначенные для того чтобы повысить производительность или функциональность в виртуальных средах.

Меняем верх строки c xml файла VM

```xml
<domain type='kvm'>
<!-- на -->
<domain xmlns:qemu="http://libvirt.org/schemas/domain/qemu/1.0" type="kvm">
```

Всё что делает hyper-v vendor_id так это устанавливает значение (value) листка 0x40000000 подменяя информацию **cpuid** в гостевой ОС на рандомно указанное вами, значения ограничены до 12 символов. По дефолту стоит "Microsoft HV" который является черным списком для EAC из-за чего вас не пускают в игру. Именно поэтому vendor_id необходимо изменить чтобы обойти это.

> Пример при использовании [pafish](https://github.com/a0rtega/pafish) (тест на обнаружение вм)

```txt
[-] Windows version: 6.2 build 9200
[-] Running in WoW64: False
[-] CPU: GenuineIntel
    Hypervisor: buttplug <- подмена на значение из vendor_id
    CPU brand:        Intel(R) Xeon(R) CPU E5-1650 0 @ 3.20GHz
```

```xml
...
<features>
  ...
  <hyperv>
    ...
    <vendor_id state='on' value='buttplug'/>
    ...
  </hyperv>
  ...
</features>
...
```

Данная строка необходима для обладателей GPU от Nvidia, она необходима для того чтобы спрятать что мы используем KVM т.к  нашу гостевую предотвращения ошибки 43


```xml
...
<features>
  ...
  <kvm>
    <hidden state='on'/>
  </kvm>
  ...
</features>
...
```

Начиная с QEMU 3.1, недавно введенный cpuid флаг Topoext отключен по умолчанию. Чтобы использовать гиперпоточность на процессорах AMD, вам необходимо вручную включить его и предоставить информацию о кэшировании:

```xml
...
  <cpu mode="host-passthrough" check="none" migratable="on">
    <topology sockets="1" dies="1" cores="5" threads="2"/>
    <cache mode="passthrough"/>
    <feature policy="require" name="topoext"/>
  </cpu>
  ...
</domain>
```

Отключаем memballoon, с ним может местами вызвать серьёзные проблемы с производительностью. Проблемы варьируются от незначительных заиканий до секундной задержки. Кроме того, memory ballooning на самом деле не работает с виртуальными машинами Vfio. Просто находим строчку `<memballoon model='virtio'>` и меняем `virtio` на `none`

```xml
   ...
    </hostdev>
    <memballoon model='none'/>
  </devices>
 ...
```

В самом конце где после `</devices>` прописываем аргументы которые могут повысить производительность и избавить от медленного интерфейса системы при включённом Hyper-V

```xml
<qemu:commandline>
    <qemu:arg value="-rtc"/>
    <qemu:arg value="base=localtime"/>
    <qemu:arg value="-cpu"/>
    <qemu:arg value="host,host-cache-info=on,kvm=off,l3-cache=on,kvm-hint-dedicated=on,migratable=no,hv_relaxed,hv_spinlocks=0x1fff,hv_vapic,hv_time,hv_vendor_id=buttplug,+invtsc,+topoext"/>
  </qemu:commandline>
```

## !!CPU Pinning!!

[Объяснение от SomeOrdinaryGamers](https://youtu.be/WYrTajuYhCk?t=752)

Для игр которые интенсивно используют CPU могут возникать

> Примечание: Согласно Arch Wiki на ядре Zen при Cpu pinning могут возникать подтормаживание и короткие зависания.

## !!Huge memory pages!!

Вставляем эти опции в параметры ядра

`default_hugepagesz=1G hugepagesz=1G hugepages=32`

## Как обойти снижение производительности при включённом Hyper-V

**Решением** для меня было добавление аргументов <qemu:commandline> в xml файл win10

Меняем верх строки c ``<domain type='kvm'>``
 на ``<domain xmlns:qemu="http://libvirt.org/schemas/domain/qemu/1.0" type="kvm">``

В самом конце где после ``</devices>`` прописываем аргументы

```xml
<qemu:commandline>
    <qemu:arg value="-rtc"/>
    <qemu:arg value="base=localtime"/>
    <qemu:arg value="-cpu"/>
    <qemu:arg value="host,host-cache-info=on,kvm=off,l3-cache=on,kvm-hint-dedicated=on,migratable=no,hv_relaxed,hv_spinlocks=0x1fff,hv_vapic,hv_time,hv_vendor_id=buttplug,+invtsc,+topoext"/>
  </qemu:commandline>
```

После чего у вас при включённом Hyper-V не будет сниженная производительность, в некоторых моментах это даже может повысить FPS в играх.\
К примеру PUBG ранее не превышал больше ~60 fps (с дропами до 40) и это на самых низких настройках графики.\
Теперь же FPS удвоился до 90 (может даже достигать до 100 fps т.е почти как нативно)
