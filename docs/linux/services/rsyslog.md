title: rsyslog

# rsyslog

rsyslogd, как и его предшественник syslogd собирают логи от различных сервисов и направляют их в log файлы или на удалённые log хосты.
Также возможна реализация отправки логов на удалённые хосты для централизации управления и улучшения практик безопасности.

Его основной конфигурационный файл - `/etc/rsyslog.conf` .

Стандартное расположение лог файлов - `/var/log` .

**Некоторые из log-файлов**:

* boot.log - содержит сообщения загрузки сервисов во время их загрузки

* ...

### rsyslog.conf

== Modules

**Секция ## MODULES ##** rsyslog.conf позволяет включать/отключать особые фичи в rsyslog. Пример такой секции в Debian 10:

```bash
#################
#### MODULES ####
#################

module(load="imuxsock") # provides support for local system logging
module(load="imklog")   # provides kernel logging support
#module(load="immark")  # provides --MARK-- message capability

# provides UDP syslog reception
#module(load="imudp")
#input(type="imudp" port="514")

# provides TCP syslog reception
#module(load="imtcp")
#input(type="imtcp" port="514")
```

Записи $ModLoad определяют загружаемые модули. Отключаются модули путём комментирования этой записи (# в начале).

== Rules

**Секция ## RULES ##** перечисляет правила по которым rsyslog будет собирать логи.

Пример этой секции в Debian 10:

``` bash
###############
#### RULES ####
###############

#
# First some standard log files.  Log by facility.
#
auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
#cron.*                         /var/log/cron.log
daemon.*                        -/var/log/daemon.log
kern.*                          -/var/log/kern.log
lpr.*                           -/var/log/lpr.log
mail.*                          -/var/log/mail.log
user.*                          -/var/log/user.log
#alert                          /dev/console


#
# Logging for the mail system.  Split it up so that
# it is easy to write scripts to parse these files.
#
mail.info                       -/var/log/mail.info
mail.warn                       -/var/log/mail.warn
mail.err                        /var/log/mail.err

#
# Some "catch-all" log files.
#
*.=debug;\
        auth,authpriv.none;\
        news.none;mail.none     -/var/log/debug
*.=info;*.=notice;*.=warn;\
        auth,authpriv.none;\
        cron,daemon.none;\
        mail,news.none          -/var/log/messages

#
# Emergencies are sent to everybody logged in.
#
*.emerg                         :omusrmsg:*
```

Записи в этой секции имеют две колонки: левая - определяет какие сообщения будут попадают под правило, правая - место, куда эти сообщения будут сбрасываться.

Сообщения определяются основываясь на сервисе (daemon, kern, user, mail и т.п.) и приоритете (начиная от debug, info, notice, продолжая warn и error, и заканчивая crit, alert и emerg), которые разделяются "." (точкой). Получается, что запись mail.warn определяет все сообщения от сервиса mail уровня warn и выше.

Стандартно все сообщения направляются в локальные файлы в `/var/log`. Но существует возможность перенаправить их на какое-либо устройство, например `/dev/console`, или на удаленный хост логирования. Значок "@" указывает имя хоста логирования (например, `@log.ak.ru`).

### Использование удалённого loghost'а с rsyslog

Для использования rsyslog для отправки логов на другую машину необходимо произвести изменения конфигурации с обоих сторон (сервера и клиента).

##### Со стороны клиента

Чтобы отправлять логи на удалённую машину, заместо файла, необходимо внести изменения в rsyslog.conf со стороны клиента. Во второй колонке секции rules заместо файла необходимо указать адрес удалённого лог-сервера через @, например:

``` bash
authpriv.*                         /var/log/secure
authpriv.*                         @log.ak.ru
```

В конкретном случае логи данного типа будут сохранятся и на локальной машине, и на удалённом сервере.

##### Со стороны сервера

На сервере необходимо разрешить приём сообщений на стандартном 514 порту TCP/UDP, для этого нужно:

* Изменить rsyslog.conf, раскоментировав строки, подгружающие imudp и imtcp модули:
``` bash
module(load="imudp")
input(type="imudp" port="514")

module(load="imtcp")
input(type="imtcp" port="514")
```

* Открыть необходимые порты на вашем системном фаерволе (iptables или firewalld).

* Перезапустить сервис rsyslog (`systemctl restart rsyslog`).

* Проверить, что сервер слушает на необходимых нам портах:
```
# netstat -tunlp | grep 514
tcp    0   0 0.0.0.0:514     0.0.0.0:*        LISTEN      1264/rsyslogd
tcp6   0   0 :::514          :::*             LISTEN      1264/rsyslogd
udp    0   0 0.0.0.0:514     0.0.0.0:*                    1264/rsyslogd
udp    0   0 :::514          :::*                         1264/rsyslogd
```


### Дополнительно

##### man

rsyslogd rsyslog.conf

##### Связанные материалы

logrotate logwatch

##### Литература

linux bible

manы

