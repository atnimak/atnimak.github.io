---
layout: post
title: Уведомление по электронной почте, при перезагрузке сервера
tags: windows email bsod
---

Столкнулся с необходимостью получать уведомления на электронную почту при перезагрузке сервера.

---
Чтобы отправлять уведомления о перезагрузке я создал в планировщике заданий Windows задачу на запуск bash файла при входе любого пользователя. Bash файл:

```bash
::Send email notifications from server
::
set mailsend-go=c:\mailsend-go-dir\mailsend-go.exe
%mailsend-go% -sub "Server was rebooted"  -smtp SMTP.SERVER.ADRESS -port SMTPPORT -ssl auth -user USERNAME -pass "PASSWORD" -from "EMAIL FROM" -to  "TO EMAIL" body -msg "Server was rebooted %date% - %time% ! Love, your SERVER ;-)"
::
```

Чтобы отправить письмо используется консольная утилита [mailsend-go](https://github.com/muquit/mailsend-go) от [muquit](https://github.com/muquit). Бинарники можно скачать со [страницы релизов](https://github.com/muquit/mailsend-go/releases), а подробная инструкция есть в [readme](https://github.com/muquit/mailsend-go/blob/master/README.md).

Есть другая, похожая утилита от этого же разработчика [mailsend](https://github.com/muquit/mailsend), но она не поддерживается с 2016 года и не показалась мне настолько же удобной.

Для установки я скачал бинарный файл со страницы релизов, положил его в папку в корне диска. Затем запустил его из коммандной строки с нужными мне аргументами:

* **-sub** указывает на тему письма
* **-smtp** и **-port** на адрес  и порт smtp сервера
* **auth**, **-user**, **-pass** позволяют авторизоваться на сервере. Обрати внимание, что **auth** является коммандой и принимает **-user** и **-pass** как аргументы
* **-from** и **-to** указывают на e-mail с которого будет отправлено письмо и адрес назначения письма
* **body** и **-msg** указывают на содержание письма. Команда **body** принимает **-msg** как аргумент. **-msg** указывает на содержание письма.

Создаем задачу в Планировщике заданий
![Панель управления NVIDIA](/assets/server-reboot/1.jpg)

Добавляем имя и описание - просто для удобства
![Панель управления NVIDIA](/assets/server-reboot/2.jpg)

Указываем триггер *при запуске компьютера* или *при входе любого пользователя*
![Панель управления NVIDIA](/assets/server-reboot/3.jpg)

Выбираем действие *Запустить программу*
![Панель управления NVIDIA](/assets/server-reboot/4.jpg)

ВыБираем пакетный файл (батник), который нам нужен
![Панель управления NVIDIA](/assets/server-reboot/5.jpg)

За скриншоты спасибо админу [salf-net.ru](https://salf-net.ru/).