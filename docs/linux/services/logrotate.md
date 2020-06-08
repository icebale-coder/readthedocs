title: logrotate

# logrotate

logrotate это сервис, который позволяет ротировать, сжимать и отправлять логи по электронной почте.

Его логично использовать на системах, которые генерируют большое количество логов, поскольку на таких машинах с течением времени логи могут начать существенное пространство на диске. 

### logrotate.conf

Основной конфигурационный файл logrotate - `/etc/logrotate.conf`. Но если в этом файле дополнительно указать `include /etc/logrotate.d`, то все файлы, находящие в этой директории, будут дополнять основной конфигурационный файл. Также в эту директорию скидывают свои конфигурационные файлы различные устанавливаемые пакеты.

Пример **основного конфигурационного** файла:

``` bash
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# your log files will be compressed
compress

# packages drop log rotation information into this directory
include /etc/logrotate.d
```

В данном случае на конкретной машине логи ротируются еженедельно, хранятся сжатыми еще в течении 4 недель. Также учитывается конфигурация из logrotate.d.

Пример **конфигурационного файла apt** из logrotate.d:

``` bash
/var/log/apt/term.log {
  rotate 12
  monthly
  compress
  missingok
  notifempty
}

/var/log/apt/history.log {
  rotate 12
  monthly
  compress
  missingok
  notifempty
}
```

В данном случае логи ротируются ежемесячно, хранятся сжатыми в течении года,  если лог файл отсутствует, то logrotate переходит к следующему без вывода ошибок, а также если лог пустой, то он не ротируется.

В начале блока этого конфигурационного файла указывается конкретный лог-файл, на который применяется эта конфигурация.

### Дополнительно

##### man'ы

- [logrotate](/linux/man/logrotate)

##### Связанные материалы

- [rsyslog](/linux/services/rsyslog)
- [logwatch](/linux/services/logwatch) - отправка на email

##### Литература

...