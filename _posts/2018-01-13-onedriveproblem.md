---
layout: post
title: Проблемы OneDrive
tags: windows onedrive problemSolved
---

#Проблема
При попытке запуска OneDrive в Windows 10 ничего не происходит. Иконка в трее отсутствует. Попытка установки ничего не даёт. 

#Помогло следующее:  

Win + R -> gpedit.msc 

Переходим
Local Computer Policy (Редактор локальной групповой политики) -> Computer Configuration (Конфигурация компьютера) -> Administrative Templates (Административные шаблоны) -> Windows Components (Компоненты Windows) -> OneDrive 

Выбираем
Prevent the usage of OneDrive for file storage (Запретить использование OneDrive для хранения файлов) -> Disabled (Отключено) 

Галочка должны быть снята. Если поставлена - снять. Перезагрузиться, попробовать установить.