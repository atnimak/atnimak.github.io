---
layout: post
title: Установка и настройка подключения по SSH
tags: windows linux ssh
---
Установка и настройка подключения по SSH.

---

- [[Настройка SSH#Установка open-ssh сервера]]
- [[Настройка SSH#Подключение по ssh]]
- [[Настройка SSH#Настройка клиента ssh]]
- [[Настройка SSH#Настройка сервера ssh]]
	- [[Настройка SSH#Теперь нам нужно передать ключь на сервер.]]
	- [[Настройка SSH#Редактируем sshd_config]]
- [[Настройка SSH#Файрвол и sshd]]
- [[Настройка SSH#SCP передача файлов]]
- [[Настройка SSH#Неудачные попытки входа]]
- [[Настройка SSH#Разное]]
	- [[Настройка SSH#Доступ только определенного пользователя к SSH]]
	- [[Настройка SSH#Выполнить локальный скрипт]]

## Установка open-ssh сервера
Если openssh-server еще не установлен и не запущен на хосте, его можно добавить с помощью следующих команд.
```
apt install openssh-server
systemctl start sshd.service
systemctl enable sshd.service
```


## Подключение по ssh
Осуществляется командой
`ssh user@host`
Например: `ssh user@192.168.1.72`, в этом случа подключение будет осуществляться на стандартный порт 22. Если ты уже изменил порт подключения на сервере, то подключаться нужно к этом порту, например: `ssh user@192.168.1.72 -p 1111`

## Настройка клиента ssh
Для начала на своей машине сгенерируем пару ключей: публичный и приватный:
```
cd ~/.ssh
ssh-keygen -t rsa
```
После ввода этой команды появится запрос на имя файла для ключей.  
А после ввода имени ключа запрос на passphrase - фразу для шифрования ключа. Ее нужно будет вводить каждый раз, когда вы обращаетесь к этому ключу.  Для реальных серверов очень рекомендуется использовать сложную фразу. Для серверов, находящихся в локальной сети, к которым нет доступа из интернета можно не использовать и оставить пустой - просто нажать Enter.

Важно обратить внимание: стандартное имя id_rsa и id_rsa_pub
Эти стандартные файлы ssh пытается открыть каждый раз, когда мы устанавливаем новое соединение с сервером. Если мы хотим создать несколько ключей для нескольких серверов,  нам нужно изменить имя ключа. Например, используем `vpn_server_keys`. Мы получили 2 файла 
```
vpn_server_keys
vpn_server_keys.pub
```
Если мы использовали стандартное имя для ключа id_rsa, то файл config можно не редактировать. Но если мы изменили имя ключа, то нужно указать в файле config, для какого сервера подходит ключ `vpn_server_keys`. Идем в 
```
nano ~/.ssh/config
```
И вносим в него информацию о сервере и имени файла ключа
```
Host github.com
 IdentityFile ~/.ssh/git_rsa

Host 25.456.73.174
 IdentityFile ~/.ssh/vpn_server_keys
```
Теперь для этих хостов ssh будет открывать указанные файлы ключа. А для всех остальных будет искать ключь в id_rsa.

Мы можем упроситить себе подключение к серверу, например, добавив в файл config не только файл ключей, но и другую информацию.  Можем создать никнейм для сервера, чтобы не набирать его ip каждый раз вручную. Например:
```
Host github.com
 IdentityFile ~/.ssh/git_rsa

Host vpnserver
 HostName 25.456.73.174
 User root
 Port 22
 IdentityFile ~/.ssh/vpn_server_keys
 IdentitiesOnly yes
```


## Настройка сервера ssh

### Теперь нам нужно передать ключь на сервер. 
Для этого Три способа:
**Первый**
Это отправить пароль на сервер с помощью команды `ssh-copy`:
```
ssh-copy-id -i ~/.ssh/id_rsa.pub user@host
```

**Второй**
Скоприровать содержимое файла одной командой
```
cat ~/.ssh/id_rsa.pub | ssh root@25.456.73.174 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod go= ~/.ssh/authorized_keys"
```

Выглядит сложно, но на самом деле это простая операция:
* Сначала вы выводите содержимое файла — `cat ~/.ssh/id_rsa.pub`
* Далее подключаетесь к удалённому хосту —  `ssh имя_пользователя@адрес_сервера`
* Затем создаёте папку и файл для хранения информации об открытых ключах.
* Затем настраиваете доступ командой `chmod -R go= ~/.ssh`
* В этом примере используется символ перенаправления ‘>>’. Он позволяет не перезаписывать содержимое файла, а дополнять его. Это удобно, если вы добавляете несколько ключей
Если работаете из-под рут, может потребоваться установить владельцем своего пользователя, а не пользователя root:
`chown -R username:username ~/.ssh`
Или дать пользователю правильные права `chmod 600 ~/.ssh/authorized_keys`

**Третий**
Которым я и воспользуюсь, перейти в папку  `~/.ssh/config`  и вручную с помощью текстового редактора вставить ключ в файл `authorized_keys`. Сначала копируем ключ из файла ключа с расширением `.pub`, а затем вставляем его в файл `authorized_keys` на сервере.  В этом файле каждая новая строка - новый публичный ключ доступа. 

На этом этапе мы уже можем подключаться к серверу без пароля, только по ключу, введя парольную фразу, если мы ее указывали.

Теперь нам нужно позаботиться о безопасности сервера. 
Переходим на сервере в `/etc/ssh/` и находим файл `sshd_config`. Будь очень внимателен, не `ssh_config` - это настройки клиента ssh. Мы ищем настройки сервера `sshd_config`.

### Редактируем sshd_config
В первую очередь меняем номер порта. По умолчанию это порт 22. 
Номер порта не должен превышать 65535. Также удостоверьтесь, что выбранное вами значение не конфликтует с другими сервисами в системе, например mysqld использует порт 3306, httpd — 80, ftpd — 21. 
В качестве номера порта вы можете использовать любой номер порта больше, чем 1024. Значения меньше 1024 являются привилегированными портами и могут использоваться только пользователем root.  Рекомендуем выбрать пятизначное значение.
```
Port 53489
```
Также можно проверить, не занят ли выбранный порт другим приложением. Скорее всего `net-tools`  у тебя уже установлен, так что переходи ко второй строке. Вывод либо покажет действующие приложения с этим портом, либо не покажет. Если вывод пуст - порт свободен.
```
apt install net-tools
# netstat -tulpan | grep 53489
```
Этой же командой можно проверить, изменился ли порт после перезагрузки сервера - теперь порт должен быть занят sshd - сервером.
Также обрати внимание на раздел [[1 - Настройка SSH#Файрвол и sshd]], если используешь файрвол на сервере.

Теперь нужно запретить доступ к пользователю root
```
PermitRootLogin no
```

Запрещаем пустые пароли
```
PermitEmptyPasswords no
```

И запрещаем доступ по паролю, чтобы доступ можно было получить только по ключу, который мы создали:
```
PasswordAuthentication no
```

Обрати внимание, чтобы была разрешена авторизация по SSH-ключам.
```
PubkeyAuthentication yes
```

Также серверу можно явно указать, где хранятся открытые ключи пользователей. Это может быть как один общий файл для хранения ключей всех пользователей (обычно это файл etc/.ssh/authorized_keys), так и отдельные для каждого пользователя ключи. Второй вариант предпочтительнее в силу удобства администрирования и повышения безопасности.

Для общего файла:
```
AuthorizedKeysFile etc/ssh/authorized_keys
```

Для каждого пользователя. Благодаря шаблону автоподстановки с маской «%h» будет использоваться домашний каталог пользователя.
```
AuthorizedKeysFile %h/.ssh/authorized_keys
```

Обратите внимание, что вариант по-умолчанию
```
AuthorizedKeysFile .ssh/authorized_keys
```

Сохраняем файл. И перезапускаем sshd сервер
`service sshd restart` или `sudo systemctl restart ssh`
Если эта команда не сработала можем просто перезапустить всю систему. Это сработает наверняка.

## Файрвол и sshd
Если на вашем компьютере работает файрвол, не забудьте добавить в его исключения новый порт, назначенный SSH-серверу. Если вы изначально работаете удалённо по SSH-протоколу, сделать это нужно ещё до того, как вы перезапустите демон SSH на сервере, к которому подключены.
Если у вас в качестве файрвола установлен UFV, выполните команду:
`sudo ufw allow 53489/tcp`

Для тех, кто использует iptables, необходимо разрешить новый порт с помощью команды:
`sudo /sbin/iptables -A INPUT -m state --state NEW -m tcp -p tcp --dport 53489 -j ACCEPT`

В операционных системах, использующих firewalld, выполните такую команду:
```
sudo firewall-cmd --permanent --add-port=53489/tcp
sudo firewall-cmd reload
```


## SCP передача файлов
После того, как мы запретили доступ без пароля у нас больше не будет работать доступ по ftp и sftp. Так что нам нужно освоить утилиту `scp`, которая позволяет перевать файлы на удаленный сервер и обратно. 

Команда чтобы передать файл на удаленный сервер
```
scp -P 3385 newtestfile.txt username@192.168.1.72:./
```
Подключение будет осуществляться с помощью ключа `ssh`, так что нужно будет подготовить passphrase. С помощью -P мы указываем порт, ведь мы его меняли.  Затем имя или адрес файла. Затем имя пользователя и адрес хоста и после двоеточия место назначения. В нашем примере это домашняя папка.

Команда чтобы передать файл с удаленного сервера на локальной устройство:
```
 scp -P 3385 username@192.168.1.72:test.txt .
```
Указываем номер порта, адрес файла на удаленном сервере (после двоеточия, после адреса хоста), и адрес назначения. В нашем случае "точка" - это значит, что положить нужно в ту директорию, в которой мы находимся.

Чтобы использовать scp с нестандартным ключом:
```
scp -P 3385 -i ~/.ssh/id_rsa.pub USER@SERVER:/home/USER/FILENAME FILENAME
```
Например:
```
scp -P 45892 -i ~/.ssh/ovh_server ubuntu@193.70.88.140:openvpn_strasburg.ovpn  C:\Users\username\Downloads
```
Или
```
 scp -P 45892 -i ~/.ssh/ovh_server ubuntu@193.70.88.140:/etc/danted.conf  C:\Users\username\Downloads
```

## Неудачные попытки входа
Также мы можем проверить неудачные попытки входа на сервер и удивиться сколько ботов стучиться к нам.
```
cat /var/log/auth.log | grep "Failed password for"
```

## Разное
### Доступ только определенного пользователя к SSH
Мы можем разрешить доступ к ssh только для определенного пользователя или группы. Для этого  добавьте строчки в файл `/etc/ssh/sshd_config`:
```
AllowUsers User1, User2, User3
AllowGroups Group1, Group2, Group3
```
Здесь User1 и Group1 - пользователь и группа к которым нужно разрешить доступ.

### Выполнить локальный скрипт
Выполним интерпретатор bash на удаленном сервере и передадим ему наш локальный скрипт с помощью перенаправления ввода Bash:
```
ssh user@host 'bash -s' < script.sh
```