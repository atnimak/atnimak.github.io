---
layout: post
title: Как установить UUID и исправить ошибку 0x80004005 UUID doesn`t match
tags: virtualbox
---

После создания снапшота или после перемещения .vhd или .vdi файла, при запуске виртуальной машины появляется ошибка 0x80004005 UUID doesn`t match

---

<script type="text/javascript" src="/public/js/jssor.slider.min.js"></script>

Текст ошибки примерно такой 
```
Fehlercode: E_FAIL (0x80004005)
Component: ProgressProxy
Interface: IProgress {c20238e4-3221-4d3f-8891-81ce92d9f913}
```
В этом случае менеджер виртуальных машин показывает ошибку, что родительский UUID снапшота не соответствует UUID диска-родителя, хранящемуся в реестре носителей `c: \ Users \ Username \ .virtualbox \ VirtualBox.xml.`

или
```
Parent UUID {00000000-0000-0000-0000-000000000000} of the medium ‘C:\Users\Username\VirtualBox VMs\XP-nik\Snapshots\{5ad80a47-8509-4b7d-9955-44bf137a77c7}.vhd’ does not match UUID {94b27e89-d561-4449-a1a7-83c2f1dd8d12} of its parent medium stored in the media registry (‘C:\Users\Username\.VirtualBox\VirtualBox.xml’).

error code:
E_FAIL (0x80004005)
```

Как и в первом случае родительский UIID снапшота не соответствует UUID диска-родителя, хранящемуся в реестре носителей `c: \ Users \ Username \ .virtualbox \ VirtualBox.xml.`

Мы можем это поправить.

Для начала нужно запустить cmd.exe от имени администратора, перейти в каталог, где установлена VirtualBox

## В первом случае
Нам нужно получить UUID родительского диска
```
D:\> vboxmanage internalcommands dumphdinfo harddisk0.vdi

--- Dumping VD Disk, Images=1
Dumping VD image "harddisk0.vdi" (Backend=VHD)
Header: Geometry PCHS=20573/16/255 LCHS=0/0/0 cbSector=512
Header: uuidCreation={b76d8026-e222-470a-9c83-bc91351bb307}
Header: uuidParent={00000000-0000-0000-0000-000000000000}
```
и свойства снапшота:
```
D:\>vboxmanage internalcommands dumphdinfo "c:\Users\UserName\VirtualBox VMs\VMName\Snapshots\{fdb2b61d-2212-45cc-8d29-b9f598d06f39}.vhd"

--- Dumping VD Disk, Images=1
Dumping VD image "c:\Users\UserName\VirtualBox VMs\VMName\Snapshots\{fdb2b61d-2212-45cc-8d29-b9f598d06f39}.vhd" (Backend=VHD)
Header: Geometry PCHS=20573/16/255 LCHS=0/0/0 cbSector=512
Header: uuidCreation={fdb2b61d-2212-45cc-8d29-b9f598d06f39}
Header: uuidParent={00000000-0000-0000-0000-000000000000}
```

Родительский UUID снапшота (00000000-0000-0000-0000-000000000000) установлен неверно. Установим правильный родительский UIID снапшоту:
```
D:\>VBoxManage.exe internalcommands sethdparentuuid "c:\Users\UserName\VirtualBox VMs\VMName\Snapshots\{fdb2b61d-2212-45cc-8d29-b9f598d06f39}.vhd" {b76d8026-e222-470a-9c83-bc91351bb307}

UUID changed to: b76d8026-e222-470a-9c83-bc91351bb307
```
Теперь виртуальная машина должна раотать верно, загружаться и последний снапшот должен быть доступен.

## Во втором случае
```
Parent UUID {00000000-0000-0000-0000-000000000000} of the medium ‘C:\Users\Username\VirtualBox VMs\XP-nik\Snapshots\{5ad80a47-8509-4b7d-9955-44bf137a77c7}.vhd’ does not match UUID {94b27e89-d561-4449-a1a7-83c2f1dd8d12} of its parent medium stored in the media registry (‘C:\Users\Username\.VirtualBox\VirtualBox.xml’).

error code:
E_FAIL (0x80004005)
```
В сообщении об ошибке, у нас уже есть UUID снапшота и правильный UUID родительского диска, нам остается только присвоить снапшоту правильный родительский UUID:
```
C:\Program Files\Oracle\VirtualBox>VBoxManage.exe internalcommands sethdparentuuid C:\Users\nik.NUTEP\VirtualBox VMs\XP-nik\Snapshots\{5ad80a47-8509-4b7d-9955-44bf137a77c7}.vhd {94b27e89-d561-4449-a1a7-83c2f1dd8d12}
```
После этого виртуальная машина должна работать правильно.

Также это решение работает, если vhd файлы вашей вируальной машины лежат не в стандартной папке виртуальной машины и снапшоты иногда теряют своих родителей.