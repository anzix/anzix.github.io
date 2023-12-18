+++
title = "!Установка RADV (vulkan-radeon) на RX 580 для Arch Linux"
date = 2023-03-07T13:09:44+05:00
draft = false
[taxonomies]
categories = []
tags = ["linux-gaming"]
+++

- [Arch Wiki - Vulkan](https://wiki.archlinux.org/title/Vulkan#Verification)

RADV это реализация vulkan драйвера от Mesa, быстро развивается и хорошо работает и даже может быть лучше amdvlk и amdgpu-pro (проприетарный драйвер).

Тесты

- [RADV vs AMDGPU-PRO](https://www.youtube.com/watch?v=m2IRBAx6JHM)

Устанавливаем Radeon Vulkan и 32 битные библиотеки

```sh
sudo pacman -S vulkan-radeon lib32-vulkan-radeon vulkan-tools
```

Для того чтобы драйвер amdgpu или (если установлен) amdgpu-pro не взяли контроль вверх над RADV необходимо добавить переменную чтобы был основным только драйвер RADV

Добавляем в `.zprofile` или `.zshenv`

```sh
export VK_ICD_FILENAMES=/usr/share/vulkan/icd.d/radeon_icd.i686.json:/usr/share/vulkan/icd.d/radeon_icd.x86_64.json
```

Обязательно перезагрузитесь чтобы переменные вступили в силу
Теперь при проверке какой драйвер используется будет показыватся только RADV

```sh
vulkaninfo | grep -i GPU
```

```txt
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
		GPU id = 0 (AMD Radeon RX 580 Series (RADV POLARIS10))
GPU id : 0 (AMD Radeon RX 580 Series (RADV POLARIS10)):
GPU0:
	deviceType        = PHYSICAL_DEVICE_TYPE_DISCRETE_GPU
```

Драйверами можно переключаться на лету, однако необходим AUR пакет [amd-vulkan-prefixes](https://aur.archlinux.org/packages/amd-vulkan-prefixes) который позволит это делать.

```sh
yay -S amd-vulkan-prefixes
```

> Примечание:
> Данной переменной можно переключатся и выбирать какой драйвер использовать программа, также это применимо к Steam прописывая в Параметрах запуска игры, тестируя на каком больше FPS.

- `vk_radv` - RADV
- `vk_amdvlk` - AMDVLK
- `vk_pro` - AMDGPU-PRO

Для того чтобы узнать какой драйвер используется для стима вводим в параметры запуска

> Примечание: это также применимо и на Wine

`DXVK_HUD=devinfo` или `DXVK_HUD=full`

![image](/images/vulkan-radeon-mesa-implementation/1658667393.png) Worms Armageddon
![image](/images/vulkan-radeon-mesa-implementation/1658744109.png) Titanfall 2
