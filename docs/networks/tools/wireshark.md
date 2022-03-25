title: Wireshark filtering

# Фильтры wireshark
Disclaimer:
```bash
...играет Марсельеза...
"All you need is dump, 
Dump is all you need..."
```

Wireshark играет в жизни сетевого инженера весьма немаловажную роль...

Здесь будут приводиться основные настройки и  фильтра, использующиеся в "routine work"

## Кастомные настройки:

### 1. Просмотр "Flow Graph"
Flow graph - позволяет отображать графически этапы взаимодействия хостов по определенному протоколу.

- English version: Statistic --> "Flow graph"
- Русская версия: "Статистика"" ---> "График потока"

Выглядет очень наглядно.

![wireshark-flow-graph](../img/wireshark-flow-graph.jpg)


### 2. Просмотр "packet diagram view"


!!!warning "Важно"
            (version 3.3.0 and above) 

В Редактировании ---> Параметры ---> Появление ---> Разметка
Выбираем следующее 

![wireshark-pdv-howto](../img/wireshark-pdv-howto.jpg)

Данная визуализация дает возможность наглядно отображать содержимое кадров в соответствии с их структурой по RFC
(заголовки кадров, пакетов, tcp,upd - сегментов, а также протоколов уровня приложений таких как http/https, quic, dhcp, ftp, smtp, mysql и всех пр...)

Прям - Агонь!!!

![wireshark-packet-diagram-view](../img/wireshark-packet-diagram-view.jpg)


## ip.src == x.x.x.x
```bash
# Выбираем пакеты с ip адресом источника = 8.8.8.8
ip.src == 8.8.8.8
```

## ip.dst == x.x.x.x
```bash
# Выбираем пакеты с ip адресом назначения = 192.168.0.111
ip.dst == 8.8.8.8
```

## ip.src == x.x.x.x || ip.dst == x.x.x.x 
или 
## ip.src == x.x.x.x or ip.dst == x.x.x.x
```bash
# Выбираем пакеты с ip адресом источника = 8.8.8.8 или с ip адресом назначения = 8.8.8.8
# Таким образом можно отфильтровать весь трафик, идущий от локального хоста по узла 8.8.8.8
ip.src == 8.8.8.8 || ip.dst == 8.8.8.8

# это - тоже самое...
ip.src == 8.8.8.8 or ip.dst == 8.8.8.8
```

## icmp
```bash
# Выбираем трафик по протоколу icmp
icmp
```

## ip.flags.df == 1
```bash
# Выбираем трафик ip трафик, в котором в заголовке выставлен в "1" флаг Don't Fragment
ip.flags.df == 1
```

## eth.src == xx:xx:xx:xx:xx:xx
```bash
# Выбираем по MAC адресу источника
eth.src == 60:a4:4c:a9:b4:b0
```

## eth.dst == xx:xx:xx:xx:xx:xx
```bash
# Выбираем по MAC адресу источника
eth.dst == 60:a4:4c:a9:b4:b0
```

## eth.src == xx:xx:xx:xx:xx:xx || eth.dst == xx:xx:xx:xx:xx:xx
```bash
# Выбираем трафик для кадров удущих "от" и "до" мака 60:a4:4c:a9:b4:b0 
eth.src == 60:a4:4c:a9:b4:b0 || eth.dst == 60:a4:4c:a9:b4:b0 
```

## Подсчет статистики по каунтерам 
Полезно для выявления наиболее активных ip источников и назначений, 
например при определении жертвы DDoS атаки или при разборе петли L2
```bash
Меню Статистика -> IPv4 Statistics -> All Addresses
```

## ! - отрицание(исключение) НЕ
не включать в результат фильтрации
```bash
# Выбрать все пакеты с ip адресом источника 8.8.8.8 
# или ip адресом назначения 8.8.8.8 
# и чтобы это не был протокол icmp
(ip.src == 8.8.8.8 || ip.dst == 8.8.8.8) && !icmp
```


## BGP
### BGP OPEN 
```bash
bgp.type == 1 
```

### BGP UPDATE 
```bash 
bgp.type == 2
```

## BFD
###  BFD Control packets
```bash
bfd
```

###  BFD echo packets
```bash
bfd_echo
```

## Создать дамп для wireshark в Linux

```bash
  "tcpdump -i enp2s0 -s 65535 -w FILENAME.pcap"
```