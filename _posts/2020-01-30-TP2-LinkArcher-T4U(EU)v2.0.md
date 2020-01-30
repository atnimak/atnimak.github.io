---
layout: post
title: Установка и настройка драйвера для TP2 Link Archer T4U (EU) v2.0 (набор микросхем RTL8812AU) под Linux Mint 18.3 или Ubuntu 16.04
tags: linux ubuntu
---

Где скачать, как установить драйвер и настроить TP2 Link Archer T4U (EU) v2.0 под Linux Mint 18.3 или Ubuntu 16.04.

---

Нам понадобится репозиторий [rtl8812au](https://github.com/gnab/rtl8812au) от [gnab.
](https://github.com/gnab)


Сменим директорию на /tmp, затем склонируем репозиторий, соберем и установим драйвер.

```bash
cd /tmp
git clone https://github.com/gnab/rtl8812au.git gnab_rtl8812au
cd gnab_rtl8812au
make
sudo insmod 8812au.ko
```

После этого мы уже сможем использовать наш адаптер, тем не менее добавим его в новые ядра, установленные в нашей системе. Настроим DKMS:

```bash
cd /tmp
sudo cp -R gnab_rtl8812au /usr/src/8812au-4.2.2
sudo dkms add -m 8812au -v 4.2.2
sudo dkms build -m 8812au -v 4.2.2
sudo dkms install -m 8812au -v 4.2.2
```
Теперь все работает как надо.