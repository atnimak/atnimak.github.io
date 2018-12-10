---
layout: post
title: Сжать динамический VHD или VDI диск
tags: virtualbox
---

Основное преимущество динамических виртуальных дисков в том, что они занимают меньше места и увеличивают свой размер в соответствии с потребностями пользователей. Однако есть и минус - в процессе роста они могут занять все доступное место, даже если внутри диска файлы удалены и есть много свободного места. В моем случае и хост и гостевая система Windows, если у вас хост и/или гостевая система Linux - эта заметка не для вас.

---

<script type="text/javascript" src="/public/js/jssor.slider.min.js"></script>

## Очищаем свободное пространство диска

Для начала сделайте бекап виртуальной машины, например, экспортируйте ее. Удалите все все снимки состояния системы, если этого не сделать можно получить ошибку. Затем удалите все лишнее на вашем виртуальном диске. Теперь скачиваем на виртуальную машину программу [Sdelete](https://technet.microsoft.com/ru-ru/sysinternals/sdelete.aspx), она небольшая и не требует установки. Sdelete окончательно очистит свободное место, записав нулевые биты во все свободное пространство диска. Итак: 

- скачиваем и кладем sdelete.exe в корень диска C:\ на вашей виртуальной машине.
- от имени администратора открываем коммандную строку, выполняем комманды

```bash
cd C:\
sdelete.exe -s -z C:
```
таким образом мы очистили раздел C на диске. Если на диске есть другие разделы, очищаем их аналогичным образом.

## Сжимаем диск

Теперь завершите работу виртуальной машины и на базовой системе откройте командную строку от имени администратора. Здесь, в зависимости от типа диска мы будем действовать по разному. 

### Ваш диск VHD

Запускаем утилиту diskpart

```bash
diskpart
```

Выбираем диск, который нам нужно сжать, указывая путь к vhd файлу:

```bash
select vdisk file="c:\Data\DAT22GB.vhd"
```

Теперь нам нужно подключить диск в режиме Read-only:

```bash
attach vdisk readonly
```

Сжимаем:

```bash
compact vdisk
```

Эта процедура может занять значительное время, в зависимости от размера виртуального диска.

Если сжатие прошло успешно, появится надпись:

```bash
DiskPart successfully compacted the virtual disk file
```

Осталось отмонтировать диск VHD:

```bash
detach vdisk
```

### Ваш диск VDI

Переходим в директорию установки VitrualBox
```bash
cd C:\Program Files\Oracle\VirtualBox
```

Указываем путь к файлу нашего диска и сжимаем его с помощью команды modifyhd и ключа compact. 

```bash
VboxManage.exe modifyhd "D:\Oracle VM VirtualBox\Windows 10 x86 Ent 1607.vdi" --compact
```
Эта процедура может занять значительное время, в зависимости от размера виртуального диска.

### Пояснения

После успешного выполнения у всех разделов диска будет забрано все пространство на котором нет файлов - именно на это пространство будет сжат файл виртуального диска. Тем не менее в самой виртуальной машине ничего не поменяется: таблица разделов останется той же, на разделах будет свободное место.