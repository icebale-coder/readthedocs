title: Linux bases

# Linux базовые понятия

## 1. Последовательность запуска системы.

### 1.2. Последовательность загрузки/загрузчик grub

### 1.3 Системы скриптов init.d/systemctl

## 2. Сигналы POSIX

## 3. Файловая система

### 3.1.Права доступа 
- [Losst ПРАВА ДОСТУПА К ФАЙЛАМ В LINUX](https://losst.ru/prava-dostupa-k-fajlam-v-linux)
- [Про SIUD бит](https://habr.com/ru/company/jetinfosystems/blog/506750/)

#### Основные права доступа к файлам
В Unix системах для файлов и директорий есть права на чтение/запись/выполнение
```bash
Read/Write/eXecute (r/w/x) (4/2/1) 
```

Для файлов и директорий есть понятие: Группа/Пользователь/Прочие
```bash
user/group/other (u/g/o)
```

#### Специальные права доступа (suid, suig, Sticky-bit)

SUID - если этот бит установлен, то при выполнении программы, id пользователя, от которого она запущена заменяется на id владельца файла. Фактически, это позволяет обычным пользователям запускать программы от имени суперпользователя;

SGID - этот флаг работает аналогичным образом, только разница в том, что пользователь считается членом группы, с которой связан файл, а не групп, к которым он действительно принадлежит. Если SGID флаг установлен на каталог, все файлы, созданные в нем, будут связаны с группой каталога, а не пользователя. Такое поведение используется для организации общих папок;

Sticky-bit - этот бит тоже используется для создания общих папок. Если он установлен, то пользователи могут только создавать, читать и выполнять файлы, но не могут удалять файлы, принадлежащие другим пользователям.

Тогда при выводе "ls -la /usr/bin/su" 
```bash
ls -la /usr/bin/su
-rwsr-xr-x 1 root root 63568 Jan 10  2019 /usr/bin/su
```

```bash
Возможные варианты прав файла/директории
Права состоят из 3 части - пользователь, группа, другие
и в каждой части по три значения,
также впереди перед тремя частями одно значение 
"-" или "l" - означает, что это ссылка на файл 
также в конце может стоять дополнительно знак "+" - 
это означает, что используются расширенные права facl (file access control list)

Возможные варианты наборов для частей:
--- - нет прав, совсем;
--x - разрешено только выполнение файла, как программы но не изменение и не чтение;
-w- - разрешена только запись и изменение файла;
-wx - разрешено изменение и выполнение, но в случае с каталогом, вы не можете посмотреть его содержимое;
r-- - права только на чтение;
r-x - только чтение и выполнение, без права на запись;
rw- - права на чтение и запись, но без выполнения;
rwx - все права;
--s - установлен SUID или SGID бит, первый отображается в поле для владельца, второй для группы;
--t - установлен sticky-bit, а значит пользователи не могут удалить этот файл.

Примеры:
"lrwxrwxrwx" - "l" - означает, что это ссылка на файл 
"-rwsrwxrwx" - "rwx" - вместо любого символа может стоять прочерк - "-", что означает, что такого права нет,
               "s" - здесь означает SUID бит
"-rwtrwxrwx" - "t" - здесь означает sticky-bit
"-rwxrwxrwx+" - знак "+" означает, что используются расширенные права facl (file access control list)
```


Пример файлов с suid битом. Те же программы su и sudo его используют.
```bash
ls -la /usr/bin/su
-rwsr-xr-x 1 root root 63568 Jan 10  2019 /usr/bin/su

ls -la /usr/bin/sudo
-rwsr-xr-x 1 root root 157192 Jan 20  2021 /usr/bin/sudo
```

#### Команды для работы с правами

##### Назначить права  - chmod
```bash
chmod
Права записываются в виде цифр
(r/w/x) (4/2/1) 
read    - 4
write   - 2
execute - 1

#Означает назначить права rwx(user) rwx(group) rwx(other) для файла FILE_NAME
chmod 777 FILE_NAME

#Как правило "стандартный" файл имеет права 644
#Означает назначить права rw(user) r(group) r(other) для файла FILE_NAME
chmod 644 FILE_NAME
```
###### Сменить пользователя/группу

```bash
# для файла MY_FILE назначить нового пользователя и новую группу
chown NEW_USER:NEW_GROUP ./MY_FILE

# для директории и файлов в ней рекурсивно назначить нового пользователя и новую группу
chown -R NEW_USER:NEW_GROUP ./MY_DIR
```

<details><summary>Пример для директории test</summary>
<p>

```bash
ls -la | grep test
drwxr-xr-x 2 root    root    4096 Aug 14 06:50 test

ls -la ./test
total 16
drwxr-xr-x 2 root    root    4096 Aug 14 06:50 .
drwxr-xr-x 8 manowar manowar 4096 Aug 14 06:50 ..
-rw-r--r-- 1 root    root       1 Aug 14 06:50 1
-rw-r--r-- 1 root    root       1 Aug 14 06:50 2

chown -R manowar:manowar ./test

ls -la | grep test
drwxr-xr-x 2 manowar manowar 4096 Aug 14 06:50 test

ls -la ./test
total 16
drwxr-xr-x 2 manowar manowar 4096 Aug 14 06:50 .
drwxr-xr-x 8 manowar manowar 4096 Aug 14 06:50 ..
-rw-r--r-- 1 manowar manowar    1 Aug 14 06:50 1
-rw-r--r-- 1 manowar manowar    1 Aug 14 06:50 2
```
</p>
</details>

### 3.2. Расширенные права facl

Можно задавать расширенный набор прав: назначать права для конкретных пользователей и групп.
```bash
setfacl - установить права
getfacl - посмотреть права
```

<details><summary>Пример использования facl</summary>
<p>

```bash

[root@server ftp]# mkdir uploads

[root@server ftp]# ls -la
итого 44
drwxr-xr-x   6 ftp  ftp  4096 Янв 26 15:51 .
drwxr-xr-x  20 root root 4096 Янв 22 14:00 ..
drwxrwxrwx+  7 ftp  ftp  4096 Янв 25 10:01 incoming
drwxr-xr-x   2 root root 4096 Янв 22 15:35 private
drwxr-xr-x   2 ftp  ftp  4096 Янв 22 13:23 pub
drwxr-xr-x   2 root root 4096 Янв 26 15:51 uploads

[root@server ftp]# setfacl -m default:user::rwx -m default:group::rx -m default:other::rx  uploads

[root@server ftp]# ls -la
итого 44
drwxr-xr-x   6 ftp  ftp  4096 Янв 26 15:51 .
drwxr-xr-x  20 root root 4096 Янв 22 14:00 ..
drwxrwxrwx+  7 ftp  ftp  4096 Янв 25 10:01 incoming
drwxr-xr-x   2 root root 4096 Янв 22 15:35 private
drwxr-xr-x   2 ftp  ftp  4096 Янв 22 13:23 pub
drwxr-xr-x+  2 root root 4096 Янв 26 15:51 uploads

[root@server ftp]# getfacl uploads
# file: uploads
# owner: root
# group: root
user::rwx
group::r-x
other::r-x
default:user::rwx
default:group::r-x
default:other::r-x

[root@server ftp]# chmod 777 uploads
[root@server ftp]# getfacl uploads
# file: uploads
# owner: root
# group: root
user::rwx
group::rwx
other::rwx
default:user::rwx
default:group::r-x
default:other::r-x

[root@server ftp]# setfacl -m default:group:advanced-users:rwx uploads
[root@server ftp]# setfacl -m default:group:adm-group:rwx uploads
[root@server ftp]# getfacl uploads
# file: uploads
# owner: root
# group: root
user::rwx
group::rwx
other::rwx
default:user::rwx
default:group::r-x
default:group:adm-group:rwx
default:group:advanced-users:rwx
default:mask::rwx
default:other::r-x

[root@server ftp]#
[root@server ftp]# cd uploads/
[root@isp8 uploads]# ls -la
итого 20
drwxrwxrwx+ 3 root  root  4096 Янв 26 16:01 .
drwxr-xr-x  6 ftp   ftp   4096 Янв 26 15:51 ..
drwxrwxr-x+ 4 fedia users 4096 Янв 26 16:01 fedia
[root@isp8 uploads]# getfacl fedia/
# file: fedia
# owner: fedia
# group: users
user::rwx
group::r-x
group:adm-group:rwx
group:advanced-users:rwx
mask::rwx
other::r-x
default:user::rwx
default:group::r-x
default:group:adm-group:rwx
default:group:advanced-users:rwx
default:mask::rwx
default:other::r-x
```
</p>
</details>

!!!warning "Важно"
			Расширенные права перекрывают набор стандартных прав.



## 4. Потоки ввода/вывода

- [Википедия - Стандартные потоки](https://ru.wikipedia.org/wiki/%D0%A1%D1%82%D0%B0%D0%BD%D0%B4%D0%B0%D1%80%D1%82%D0%BD%D1%8B%D0%B5_%D0%BF%D0%BE%D1%82%D0%BE%D0%BA%D0%B8)
- [Перенаправление ввода вывода в Linux](https://community.vscale.io/hc/ru/community/posts/115000171385-%D0%9F%D0%B5%D1%80%D0%B5%D0%BD%D0%B0%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B2%D0%B2%D0%BE%D0%B4%D0%B0-%D0%B2%D1%8B%D0%B2%D0%BE%D0%B4%D0%B0-%D0%B2-Linux)

Стандартные потоки ввода-вывода в системах типа UNIX — потоки процесса, имеющие номер (дескриптор), зарезервированный для выполнения некоторых «стандартных» функций. Как правило, эти дескрипторы открыты уже в момент запуска исполняемого файла.

### Стандартный ввод - stdin
```bash
"stdin" - имеет дескриптор номер "0", 
предназначен для чтения команд пользователя или входных данных.
```
Можно перенаправлять данные в этот поток с помощью символа "<".

### Стандартный вывод - stdout
```bash
"stdout" - имеет дескриптор номер "1", предназначен для вывода данных, 
как правило (хотя и не обязательно) текстовых.
```
Можно перенаправлять данные из этого потока с помощью символа ">".

### Стандартный вывод ошибок - stderr
```bash
"stderr" - имеет дескриптор номер "2", предназначен для вывода 
диагностических и отладочных сообщений в текстовом виде.
```
Можно перенаправлять данные из этого потока с помощью символа "2>".

### Перенаправление потоков  " > < >> << | "

#### Примеры работы с потоками
```bash
---------------------------------------

В "stdin" можно перенаправлять данные с помощью символа "<".

#перенаправить в ввод программы test.sh содержимого файла /var/log/messages
test.sh < /var/log/messages

---------------------------------------

Из "stdout" можно перенаправлять данные с помощью символа ">".

#перенаправить вывод ошибок в /dev/null, таким образом не отображать их.
test.sh 2> /dev/null

#перенаправить stdout в /dev/null и перенаправить stderr в место, куда указывает stdout
#таким образом stdout и stderr  отпавляются в /dev/null
test.sh > /dev/null 2>&1

---------------------------------------

"|" -"pipe", "труба" - возможность перенаправления "stdout" одной программы в "stdin" другой программы

# поиск в содержимом файла /var/log/messages вхождения слова "error"
# или другими словами, что происходит с точки зрения потоков
# Вывести содержимое файла /var/log/messages и перенаправить вывод программы cat на ввод программы grep
cat /var/log/messages | grep error
--------------------------

">> <<" - перенаправление без затирания уже существующего там, а просто добваление туда
```

типы файлов: pipe,socket

## 5. Вход с систему, ограничения доступа

На примере Debian

### 5.1. Настройка ssh

#### Ограничение входа под root по ssh

С версии OpenSSH 7.0 поменялись дефолтовые параметры входа для пользователя root по ssh.

Теперь по дефолту стоит "PermitRootLogin prohibit-password".
Что означает, что удалённый вход под пользователем root с парольной и интерактивной аутентификацией теперь запрещён.

Для разрешения входа под root по ssh необходимо исправить строку в конфиге /etc/ssh/sshd_config и перезагрузить сервис.
```bash
	PermitRootLogin yes
```
#### Настройка входа по ключу под root-ом

https://losst.ru/avtorizatsiya-po-klyuchu-ssh

Для входа под root-ом по ключу ssh необходимо добавить  публичный ключ пользователя
в директорию /root/.ssh/authorized_keys 

Изначально директории /root/.ssh нет, п.э. её надо создать

```bash
cd /root
mkdir ./ssh
echo 'PUBLIC_USER_KEY' > /root/.ssh/authorized_keys
```
P.S. Чтобы пользоваться входом по ключу со стороны клиента ssh надо указать свой приватный ключ для этого соединения.

#### Настройка входа по ключу под обычной учеткой
Для входа под учеткой USERNAME по ключу ssh необходимо добавить свой публичный ключ 
в директорию пользователя /home/USERNAME/.ssh/authorized_keys.

```bash
cd /home/USERNAME
mkdir ./ssh
echo 'PUBLIC_USER_KEY' > /home/USERNAME/.ssh/authorized_keys
```
P.S. Чтобы пользоваться входом по ключу со стороны клиента ssh надо указать свой приватный ключ для этого соединения.

### 5.2. Повышение привелегий через su sudo

#### su

"su" - Позволяет заходить обыкновенному пользователю под "superuser" - пользователем root, 
введя соответственно его пароль

Алгоритм работы при использовании su:
```bash
1. Авторизуемся под своей учетной записью
2. Авторизуемся через программу su под root пользователем.
```

#### sudo 

Развернутая статья по su и sudo на [хабре](https://habr.com/ru/post/44783/)

"sudo" - Программа позволяющая более тонко настраивать права доступа по сравнению с командой "su"
Теперь администратор может указывать список разрешенных команд для каждого пользователя (или группы пользователей), файлы, доступные для редактирования, специальные переменные окружения и многое другое (все это великолепие управляется из /etc/sudoers, см. man sudoers от своего дистрибутива). При запуске sudo спрашивает у пользователя его собственный пароль, а не пароль root.

для упрощения доступа и если нет необходимости в введении доп. пароля при получении прав root
в файле /etc/sudoers
```bash
# Allow members of group sudo to execute any command
%sudo<->ALL=(ALL:ALL) NOPASSWD:ALL
```
Приемущество изначального входа под учеткой пользователя заключается в его логировании при входе под своей учеткой.

```bash
"sudo su -" vs "sudo -i"

"sudo su -" - полностью выполняется логин от имени root, наследует окружение root-а.

"su -" - команда загружает окружение именно root пользователя.

"sudo -i" -  логин эмулируется - не наследуется окружение пользователя root.

P.S. "su" и "sudo -i" - не загружают окружение пользователя root
```

#### Ограничение входа - вход только по ключу
Для разрешения входа под root по ssh необходимо исправить строку в конфиге /etc/ssh/sshd_config и перезагрузить сервис.

```bash
	PermitRootLogin no
```
P.S. как было описано выше с версии OpenSSH 7.0 дефолтовые параметры входа для пользователя root по ssh
"PermitRootLogin prohibit-password"  - При указании prohibit-password допускается только аутентификация по ключам или через GSSAPI.

При параметре "PermitRootLogin no" даже при наличии ключа для учетки root нельзя будет под ним авторизоваться.

root будет доступен только с физической или KVM консоли.

## 6. Файерволл

iptables

firewalld

## 7. Базовый набор команд

## 8. Cистема репозитариев/работа с ними

На примере Debian

apt, apt-get, dpkg