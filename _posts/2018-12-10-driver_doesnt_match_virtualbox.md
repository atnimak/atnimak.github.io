---
layout: post
title: Ошибка при обновлении virtualbox
tags: linux virtualbox
---

# Ошибка driver doesn`t match при обновлении virtualbox

После очередного обновления virtualbox при запуске виртуальной машины появляется окно с ошибкой

```
VERR_VM_DRIVER_VERSION_MISMATCH (-1912) - The installed support driver doesn't match the version of the user.
```

---

<script type="text/javascript" src="/public/js/jssor.slider.min.js"></script>

# Решение  

Удаляем VirtualBox и очищаем систему от старых пакетов и зависимостей:

```bash
sudo apt remove --purge virtualbox virtualbox* && sudo apt clean && sudo apt autoremove && clear
```

После этого заново устанавливаем нужную версию VirtualBox.

Также давайте проверим все ли в порядке с ядром kernel:

```bash
sudo /sbin/vboxconfig
```

Результат должен быть примерно таким:

```bash
vboxdrv.sh: Building VirtualBox kernel modules.
vboxdrv.sh: Starting VirtualBox services.
```

Также можно попробовать перенастроить пакет VirtualBox:

```bash
sudo dpkg-reconfigure virtualbox-X.X
```

Обязательно проверьте включена ли аппаратная виртуализация в BIOS.