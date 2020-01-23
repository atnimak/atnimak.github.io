---
layout: post
title: Импорт и экспорт виртуальных машин с помощью командной строки
tags: virtualbox
---

С экспортом и импортом виртуальных машин через графический интерфейс [мы разобрались.]({% post_url 2018-12-11-import-export-vm %}) Теперь разберемся, как сделать это через командную строку.

---

Во-первых используем команду **vboxmanage list vms**, чтобы определить название экспортируемой виртуальной машины

В Linux это будет выглядеть вот так:

```bash
$ vboxmanage list vms 

"W7x64" {99378e99-d5c4-4bea-87ab-ca5ab28febea} 
"W10x64" {409eaa40-59c2-4259-9188-eef7479f1b91}
"Ubuntu" {e9aa10d9-8aa3-4186-a39b-014b2c3589dc}
```


Под Windows вот так:

```bash
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe list vms 

"W7x64" {99378e99-d5c4-4bea-87ab-ca5ab28febea} 
"W10x64" {409eaa40-59c2-4259-9188-eef7479f1b91}
"Ubuntu" {e9aa10d9-8aa3-4186-a39b-014b2c3589dc}
```

Предположим, что мы хотим экспортировать виртуальную машину "W7x64". Для этого используем **vboxmanage export**.

В Linux:

```bash
$ vboxmanage export W7x64 -o /media/machines/W7x64.ova
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100% 
```

В Windows:

```bash
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe export W7x64 -o d:\W7x64.ova
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100% 
```

Для импорта виртуальной машины мы будем использовать **vboxmanage import**.

В Linux:

```bash
$ vboxmanage import /media/machines/W7x64.ova
```

В Windows:

```bash
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe import /media/machines/W7x64.ova
```

Опция **-n** предложит параметры импорта. В любой непонятной ситуации используй **vboxmanage --help** :)