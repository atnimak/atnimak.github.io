---
layout: post
title: Иногда возникают ошибки и синие экраны, причиной которых являются драйвера Nvidia
tags: windows nvidia bsod
---

Иногда возникают синие экраны и ошибки, причиной которых являются драйвера Nvidia. Проблему можно решить и 
решение проще, чем кажется.

---
Нам нужно запомнить во время работы какого приложения или игры возникает BSOD, затем открыть

Панель управления Nvidia -> Управление параметрами 3D

Затем переходим на вкладку *Программные настройки*.
- В пункте 1. в списке находим нужную нам программу. Если программы нет в списке, нажимаем *Добавить* и указываем путь к исполняемому файлу. 
- В пункте 2. выбираем *Высокопроизводительный процессор NVIDIA*. 
- В пункте 3. ищем *Режим управления электропитанием* и вместо *Авто* выбираем *Предпочтителен режим максимальной прозводительности*. 
 
Все! На всякий случай можно перезагрузиться.

![Панель управления NVIDIA](/assets/nvidiabsods/nvidiapanel.png)

