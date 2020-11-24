---
layout: post
title: Windows Terminal установка и настройка
tags: windows
---
Оказывается Microsoft выпустили неплохой интерфейс для командной строки, который поддерживает cmd и PowerShell. Это заметка про его установку и настройку.

---

Скачать [Windows Terminal](https://docs.microsoft.com/ru-ru/windows/terminal/get-started) можно установить, как из магазина приложений [Windows](https://www.microsoft.com/ru-ru/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab), так и скачать с [Github](https://github.com/microsoft/terminal/releases), хотя в этом случае обновлять его придется вручную. 

После установки я настроил себе тему [Dracula](https://draculatheme.com/windows-terminal/). Для этого нужно отредактировать файл settings.json.

Также мне очень хотелось иметь контекстное меню "Open Terminal here" в проводнике Windows. Из коробки такая опция не предоставляется, но если очень захотеть, можно в космос полететь. Обсуждение этой опции [вот здесь](https://github.com/microsoft/terminal/issues/1060), решение [вот здесь](https://github.com/microsoft/terminal/issues/1060#issuecomment-497539461). Итак, работать дальше будем в cmd, а не в PowerShell.  Проверим, работают ли у нас переменные:
```
echo %USERPROFILE%
echo %LOCALAPPDATA%
```
Если команда `echo` выводит правильные паки:
%USERPROFILE% → C:\Users\[userName]
%LOCALAPPDATA% → C:\Users\[userName]\AppData\Local
То все в порядке.  Для начала создадим папку, куда мы положим иконку:
```
mkdir "%USERPROFILE%\AppData\Local\terminal"
```
Затем скопируем в эту папку, свежесозданную папку, иконку **wt_32.ico** из [репозитория Microsoft](https://github.com/yanglr/WindowsDevTools/tree/master/awosomeTerminal/icons).

Теперь создадим файл **wt.reg** следующего содержания. И запустим от имени администратора. 
```
[HKEY_CLASSES_ROOT\Directory\Background\shell\wt]
@="Windows terminal here"
"Icon"="%USERPROFILE%\\AppData\\Local\\terminal\\wt_32.ico"

[HKEY_CLASSES_ROOT\Directory\Background\shell\wt\command]
@="%LOCALAPPDATA%\\Microsoft\\WindowsApps\\wt.exe"
```
После этого все должно работать. При нажатии правой кнопкой в проводнике должно появиться контекстное меню "Windows Terminal here". Однако в моем случае сценарий дал сбой и не сработала переменная %LOCALAPPDATA%, поэтому я исправил содержание файла **wt.reg** на:
```
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\Directory\Background\shell\wt]
@="Windows terminal here"
"Icon"="%USERPROFILE%\\AppData\\Local\\terminal\\wt_32.ico"

[HKEY_CLASSES_ROOT\Directory\Background\shell\wt\command]
@=C:\\Users\\Maxim\\AppData\\Local\\Microsoft\\WindowsApps\\wt.exe"
```
К слову, в зависимости от версии Windows Terminal которую вы используете вам может потребоваться исправить *wt.exe* на *wtd.exe* в файле реестра выше. 

Теперь все действительно работает: контекстное меню появилось, терминал запускается. Только запускается он в стандартной директории, а не в той, в который вы пытаетесь его запустить. Это следствие недавнего обновления. Чтобы все-таки добиться желаемого исправьте settings.json и в разделе profiles добавьте переменную "startingDirectory" : "." Именно так, значение переменной должно быть "." 

На всякий случай прикладываю мой [settings.json](../assets/windowsTerminal/settings.json).
