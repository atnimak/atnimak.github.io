---
layout: post
title: Заметка  о clonehd, копировании и о том, как мерджить снапшоты
tags: virtualbox
---

Немного о clonehd. Эта комманда клонирует виртуальный диск, но в отличии от ручного копирования присваивает ему UUID, чтобы скопированный диск можно было подключить в менеджере виртуальных носителей избежать проблемы описанной [вот здесь](https://atnimak.github.io/2018/12/10/how-to-set-newuuid/). Также эта команда позволяет быстро превратить снимок системы (снапшот) в новый виртуальный диск. Смерджить изменения, так сказать.

---

<script type="text/javascript" src="/public/js/jssor.slider.min.js"></script>

Итак, чтобы скопировать виртуальный диск используем команду clonehd.

```bash
VBoxManage clonehd         <uuid>|<filename> <outputfile>
                           [--format VDI|VMDK|VHD|RAW|<other>]
                           [--variant Standard,Fixed,Split2G,Stream,ESX]
                           [--type normal|writethrough|immutable]
                           [--remember]
```
**format** — позволяет выбрать формат файла для выходного файла, если формата исходного файла отличается.
**variant** — позволяет указать разновидность формата выходного файла. Указывается в виде списка значений разделенных запятыми. В случае указания не совместимых комбинаций получим сообщение об ошибке.
**type** — Применяется только если указан также параметр —remember. Определяет нужный тип образа жесткого диска.
**remember** — После успешного создания образа, регистрирует его в менеджере виртуальных носителей.

Копируем образ, если в работаете в Windows:

```bash
"C:\Program Files\VirtualBox\VBoxManage.exe" clonehd "D:\VirtualBox\pfsense.vdi" "D:\VirtualBox\pfsense2.vdi"
```

Копируем образ, если в работаете в Linux:

```bash
VBoxManage clonehd /home/user/.VirtualBox/HardDisks/Win.vdi /home/user/data/Win.vdi
```

Также clonehd позволяет скопировать файл снапшота в файл виртуального диска, задодно смерджив все изменения состояния виртуальной машины до этого снапшота:

В Windows:
"C:\Program Files\VirtualBox\VBoxManage.exe" clonehd D:\VirtualBox\{uuid-of-last-snapshot} D:\VirtualBox\thediskfull.vdi

В Linux
```bash
VBoxManage clonehd fullpath/{uuid-of-last-snapshot}.vdi thedisk-full.vdi
```
Если вы не хотите пользоваться коммандной строкой вы можете воспользоваться интерфейсом VirtualBox, чтобы склонировать снапшот откройте интерфейс вашей VirtualBox, выберите нужный снапшот, нажмите правой кнопкой мыши и выберите *Клонировать*. Чтобы скопировать диск зайдите в *Менеджер виртуальных носителей* вашей VirualBox, выберите файл виртуального диска и нажмите *Копировать*. В обоих случаях укажите место и имя файл выходного диска и выберите *Полное копирование* в соответствующих диалогах.