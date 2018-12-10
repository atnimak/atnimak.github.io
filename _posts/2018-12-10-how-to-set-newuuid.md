---
layout: post
title: Как установить UUID и исправить ошибку UUID already exists
tags: virtualbox
---

После  перемещения .vhd или .vdi файла, при запуске виртуальной машины появляется ошибка Cannot register the hard disk because a hard disk with UUID already exists.

---

<script type="text/javascript" src="/public/js/jssor.slider.min.js"></script>

Данная ошибка возникает обычно после перемещения файла виртуального диска и попытки добавить перемещенный диск к существующей виртуальной машине. Чтобы исправить ошибку нам нужно задать виртуальному диску другой UUID, для этого:

- открываем командную строку от имени администратора
- переходим в папку с VirtualBox:

```bash
cd "C:\Program Files\Oracle\Virtualbox"
```

- задаем файлу виртуального диска новый UUID:

```bash
vboxmanage internalcommands sethduuid "C:\folder\win7.vdi"
```
В случае успеха командная строка выведет:

```bash
UUID changed to:...
```

После этого заходите в интерфейс VirtualBox и прикрепляйте перемещенный диск, вместо существовавшего диска.
Перед перемещением диска лучше бы удалить все снимки системы, но если вы этого не сделали и при попытке запуска получили ошибку 0x80004005 UUID doesn`t match обратитесь [к этой статье](/2018-12-6-how-to-set-uuid.md).