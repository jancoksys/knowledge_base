---
description: информация о применяемых тактиках, инструментах и техниках для взлома машин
---

# Try hack me

## Общие инструменты

* `gobuster` - позволяет проверять наличие директорий на сайте (брутфорсом)
* `sql injection` - смотреть стандартные формы ввода данных (например php), искать уязвимости в коде, одна из уязвимостей - знак комментария в поле логина/пароля (комментирует часть php кода, например игнорирует пароль)
* `RFI` - удаленное включение файла (передать параметром URL после `?page=`)
* `LFI` - искать локальные файлы на сервере (идти вниз по папкам на сервере через ../../../ после `?page=`)
* `responder` - создает фейковые сервера, слушает хендшейки (например NTLM), перехватывает хэши
* `john the ripper` - брутфорс хэшей
* `strings` - показывает все читаемые строки в бинарном файле
* Различные reverse shell\
  [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
*

### Nmap

`-p-` - скан всех портов

`-Pn` - игнор пинга (nmap пингует машину перед полноценным сканированием для более быстрого скана, но некоторые фаерволы на машинах блокируют пинг)

`-sC` - сканирование с применением скриптов (может даже обнаружить дополнительную инфу о сервисе, нужно гуглить скрипты)

`-sV` - скан версии сервиса

`-v -vv -vvv` - расширенный вывод инфы о сканировании

`-T1 -T2 -T3 -T4 -T5` - ускорение сканирования

## Basic Pentesting

* [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum) - тоже самое что и LinPeas (скан на эскалацию привилегий)
* **ssh2john** - если найдем приватный ключ RSA для SSH, можно перевести ключ в хэш и далее взламывать с помощью john the ripper

## Linux PrivEsc

* `mkpasswd -m sha-512 pass123` - делает хэш пароля (можно записать в /etc/shadow)
* `openssl passwd pass321` - тоже генерирует хэш (можно записать в /etc/passwd)
* `sudo -l` - выводит инфу о командах, которые данный юзер может выполнить как рут
* [https://gtfobins.github.io/#](https://gtfobins.github.io) - гайд как стать рутом с помощью команды которая доступна для выполнения через sudo

## Simple CTF

* `searchsploit` - консольный интерфейс поиска уязвимостей и CVE для различных сервисов
* `vim :Sex :Vex` - файловый менеджер внутри вима
* `ffuf -maxtime 360 --ignore-body` - ускорение процесса брута

## Pickle rick CTF

* [https://github.com/JohnHammond/poor-mans-pentest](https://github.com/JohnHammond/poor-mans-pentest) - скрипты для удобного применения команд (стабильный шелл, передача файлов по netcat)
*   [https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) - удобное сканирование файлов без пароля/подозрительных/потенциальных для пентеста



## Vulneversity CTF

* найти http сервер через nmap&#x20;
* найти с помощью gobuster директорию для загрузки файла на сервер
* проверить возможность php reverse shell&#x20;

`.php` нельзя загрузить, использовать burp suite intruder чтобы перебрать все возможные .php расширения для загрузки файла&#x20;

### BurpSuite Indtruder

* перехватить запрос через proxy&#x20;
* отправить запрос в intruder
* в теле запроса отметить знаком параграфа место которое будет заменяться на кастомный payload &#x20;
* привести список строк для payload&#x20;
* провести атаку, в теле html определить удачное расширение (.phtml)\


предварительно в теле php reverse shell файла указать vpn ip и желаемый порт

`nc (netcat)` - открывает tcp соединение на выбранном порту

* открыть соединение на прослушку на указанном порте
* перейти в браузере к отправленному shell на сервер
* в консоли netcat появится shell сервера

для privilege escalation (повышение до рута) нужно определить файлы, которые можно выполнять от имени рута (SUID=4000) командой find&#x20;

* нашли файл systemctl (позволяет запускать процессы - потенциальная уязвимость)
* можно создать процесс с любой полезной нагрузкой, так как будет выполнено с правами рута
* создать процесс, запускающий root shell через bash скрипт на некотором порту
* запустить процесс через systemctl enable и затем start
* запустить nc на kali на ip машины и нужном порту

## Kenobi CTF

### SUID SGID Sticky Bit

![](https://lh3.googleusercontent.com/KNpf55O2Nq4F1QbGMy4G-LR4zGTZwEQisF3s42pJT4Gd8xl-1r0pXj1qWPkbvJMETeBtsnal4xeTIMZYMhYiRVj0pnzcW85SCi-CAaQ8tDae4M-y1-yzMkm21nyjYlQyKpLNmAI5o9zENkOV\_KDVkp8)

| Permission | On Files                                                       | On Directories                                            |
| ---------- | -------------------------------------------------------------- | --------------------------------------------------------- |
| SUID Bit   | User executes the file with permissions of the file owner      | -                                                         |
| SGID Bit   | User executes the file with the permission of the group owner. | File created in directory gets the same group owner.      |
| Sticky Bit | No meaning                                                     | Users are prevented from deleting files from other users. |

## Mr  robot CTF

* ```bash
  python -c 'import pty; pty.spawn("/bin/bash")'
  ```

заспавнить нормальный терминал через питон (чтобы использовать команду `su` для переключение пользователя)

* эксплойт NMAP, если на него установлен SUID

```
nmap --interactive
!bash
whoami
exit
!sh
whoami
```

