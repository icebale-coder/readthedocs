title: Commands

Disclaimer 1:
```bash
There are a lot of them here...
Enjoy!!! )
```

Disclaimer 2:
```bash
"Староверы" vs "Провосланые" в Linux
"Oldschool" vs "Newage"
"Oldfag" vs "Newfag"
"Хиппи" vs "Хипстеры"
(...кто как хочет - так и %рочит...)

сорры фор май инглыш...
```

## Базовый набор команд/утилит

### ps
```bash
"ps -ef"
```

### netstat
```bash
#Показывает список соединений

"netstat" - deprecated 
Заменяем на "ss"

Примеры использования
"netstat -anp | grep EST"
```

### ss
```bash
#Показывает список соединений
"ss"
```

### ifconfig
```bash
"ifconfig" - deprecated 
Заменяем на "ip add"

Примеры использования
"ifconfig"
```

### arp
```bash
"arp" - deprecated 
Заменяем на "ip n"

Примеры использования
"arp"
```

### route
```bash
"route" - deprecated 
Заменяем на "ip route"

Примеры использования
"route -n"
```


### ip

Универсальная программа, которая пришла на смену ifconfig, arp, route

Программа "ip" из пакета "iproute2".

Пакет iproute2 позволяет в том числе  настраивать маршрутизацию от источника.

```bash
"ip add"
"ip n"
"ip route"
```

### Фильтрация 
```bash
sort
grep
awk
```

### Потоки/перенаправление ввода-вывода

```bash
"stdin"  = 0
"stdout" = 1
"stderr" = 2
"/dev/null" - "черная дыра" )
">" - перенаправление вывода
"<" - перенаправление ввода
"|" - перенаправление stdout из одной программы в stdin другой 
"<<" - - перенаправление ввода без затирания уже содержимого там
">>" - перенаправление вывода без затирания уже содержимого там

"1>/dev/nul 2>&1" = "1>/dev/null 2>/dev/null"
- перенаправление stdout в /dev/nul и последующего перенаправление в stdout потока stderr 
```

### conntrack
```bash
# Просмотр инфы по таблице натирования для ip адреса x.x.x.x
"conntrack -L | grep x.x.x.x "
```

### stress-ng
[Прожиг железа strees-ng](https://redos.red-soft.ru/base/manual/redos-manual/utilites/hardware-stress-test/stress-ng/)

```bash
#Тестирование процессора
"stress-ng --cpu 16 --cpu-method matrixprod --metrics --timeout 60"

#Тестирование дисковой подсистемы
"stress-ng --sequential 0 --class io --timeout 60s --metrics-brief"

#Тестирование памяти
"stress-ng --sequential 0 --class memory --timeout 60s --metrics-brief"

# Комплексное тестирование
Эта команда запустит тест для CPU в 8 потоков, тест виртуальной памяти 
с размещением в ней одного гигабайта данных, 
а также 4 стрессора для тестирования операций ввода/вывода.

"stress-ng --cpu 4 --io 4 --vm 1 --vm-bytes 1G --timeout 60s --metrics-brief"

#Чтобы запустить тестирование всего «железа», используется команда
Эта команда запустит все тесты. После выполнения результат будет выведен в консоль. 
Во время выполнения команды лучше компьютер не трогать

"stress-ng --sequential 0 --timeout 60s --metrics-brief"
```

### bgpq4 
Полезная программа для генерации префиксов которые есть в AS, согласно БД RIR

Как "готовить" bgpq4 смотрите [здесь](https://icebale.readthedocs.io/en/latest/linux/Debian/services/make-program/)

Примеры использования - построения префиксов из БД RIPE Орион-Телекома AS31257

Cisco IOS/ IOS XE

<details><summary>bgpq4 -l PL-ORION AS31257</summary>
<p>

```bash
no ip prefix-list PL-ORION
ip prefix-list PL-ORION permit 80.65.16.0/20
ip prefix-list PL-ORION permit 80.65.16.0/21
ip prefix-list PL-ORION permit 80.65.16.0/24
ip prefix-list PL-ORION permit 80.65.20.0/24
ip prefix-list PL-ORION permit 80.65.24.0/21
ip prefix-list PL-ORION permit 81.25.76.0/22
ip prefix-list PL-ORION permit 81.25.76.0/23
ip prefix-list PL-ORION permit 81.25.76.0/24
ip prefix-list PL-ORION permit 81.25.77.0/24
ip prefix-list PL-ORION permit 81.25.78.0/23
ip prefix-list PL-ORION permit 81.25.78.0/24
ip prefix-list PL-ORION permit 81.25.79.0/24
ip prefix-list PL-ORION permit 86.62.6.0/24
ip prefix-list PL-ORION permit 94.73.192.0/18
ip prefix-list PL-ORION permit 94.73.192.0/19
ip prefix-list PL-ORION permit 94.73.202.0/24
ip prefix-list PL-ORION permit 94.73.224.0/19
ip prefix-list PL-ORION permit 94.73.240.0/20
ip prefix-list PL-ORION permit 94.73.240.0/24
ip prefix-list PL-ORION permit 94.73.241.0/24
ip prefix-list PL-ORION permit 94.73.242.0/24
ip prefix-list PL-ORION permit 94.73.243.0/24
ip prefix-list PL-ORION permit 94.73.244.0/24
ip prefix-list PL-ORION permit 94.73.245.0/24
ip prefix-list PL-ORION permit 94.73.246.0/24
ip prefix-list PL-ORION permit 94.73.247.0/24
ip prefix-list PL-ORION permit 94.73.248.0/24
ip prefix-list PL-ORION permit 94.73.249.0/24
ip prefix-list PL-ORION permit 94.73.250.0/24
ip prefix-list PL-ORION permit 94.73.251.0/24
ip prefix-list PL-ORION permit 94.73.252.0/24
ip prefix-list PL-ORION permit 109.226.192.0/18
ip prefix-list PL-ORION permit 109.226.192.0/19
ip prefix-list PL-ORION permit 109.226.224.0/19
ip prefix-list PL-ORION permit 134.19.128.0/21
ip prefix-list PL-ORION permit 171.33.248.0/21
ip prefix-list PL-ORION permit 171.33.250.0/23
ip prefix-list PL-ORION permit 171.33.252.0/23
ip prefix-list PL-ORION permit 176.116.160.0/20
ip prefix-list PL-ORION permit 176.116.160.0/21
ip prefix-list PL-ORION permit 176.116.168.0/21
ip prefix-list PL-ORION permit 185.84.172.0/23
ip prefix-list PL-ORION permit 185.84.172.0/24
ip prefix-list PL-ORION permit 185.84.173.0/24
```
</p>
</details>

Cisco XR

<details><summary>bgpq4 -X -l PL-ORION AS31257</summary>
<p>

```bash
no prefix-set PL-ORION
prefix-set PL-ORION
 80.65.16.0/20,
 80.65.16.0/21,
 80.65.16.0/24,
 80.65.20.0/24,
 80.65.24.0/21,
 81.25.76.0/22,
 81.25.76.0/23,
 81.25.76.0/24,
 81.25.77.0/24,
 81.25.78.0/23,
 81.25.78.0/24,
 81.25.79.0/24,
 86.62.6.0/24,
 94.73.192.0/18,
 94.73.192.0/19,
 94.73.202.0/24,
 94.73.224.0/19,
 94.73.240.0/20,
 94.73.240.0/24,
 94.73.241.0/24,
 94.73.242.0/24,
 94.73.243.0/24,
 94.73.244.0/24,
 94.73.245.0/24,
 94.73.246.0/24,
 94.73.247.0/24,
 94.73.248.0/24,
 94.73.249.0/24,
 94.73.250.0/24,
 94.73.251.0/24,
 94.73.252.0/24,
 109.226.192.0/18,
 109.226.192.0/19,
 109.226.224.0/19,
 134.19.128.0/21,
 171.33.248.0/21,
 171.33.250.0/23,
 171.33.252.0/23,
 176.116.160.0/20,
 176.116.160.0/21,
 176.116.168.0/21,
 185.84.172.0/23,
 185.84.172.0/24,
 185.84.173.0/24
end-set
```
</p>
</details>

Juniper

<details><summary>bgpq4 -J -l PL-ORION AS31257</summary>
<p>

```bash

policy-options {
replace:
 prefix-list PL-ORION {
    80.65.16.0/20;
    80.65.16.0/21;
    80.65.16.0/24;
    80.65.20.0/24;
    80.65.24.0/21;
    81.25.76.0/22;
    81.25.76.0/23;
    81.25.76.0/24;
    81.25.77.0/24;
    81.25.78.0/23;
    81.25.78.0/24;
    81.25.79.0/24;
    86.62.6.0/24;
    94.73.192.0/18;
    94.73.192.0/19;
    94.73.202.0/24;
    94.73.224.0/19;
    94.73.240.0/20;
    94.73.240.0/24;
    94.73.241.0/24;
    94.73.242.0/24;
    94.73.243.0/24;
    94.73.244.0/24;
    94.73.245.0/24;
    94.73.246.0/24;
    94.73.247.0/24;
    94.73.248.0/24;
    94.73.249.0/24;
    94.73.250.0/24;
    94.73.251.0/24;
    94.73.252.0/24;
    109.226.192.0/18;
    109.226.192.0/19;
    109.226.224.0/19;
    134.19.128.0/21;
    171.33.248.0/21;
    171.33.250.0/23;
    171.33.252.0/23;
    176.116.160.0/20;
    176.116.160.0/21;
    176.116.168.0/21;
    185.84.172.0/23;
    185.84.172.0/24;
    185.84.173.0/24;
 }
}
```
</p>
</details>

Huawei

<details><summary>bgpq4 -U -l PL-ORION AS31257</summary>
<p>

```bash
undo ip ip-prefix PL-ORION
ip ip-prefix PL-ORION permit 80.65.16.0 20
ip ip-prefix PL-ORION permit 80.65.16.0 21
ip ip-prefix PL-ORION permit 80.65.16.0 24
ip ip-prefix PL-ORION permit 80.65.20.0 24
ip ip-prefix PL-ORION permit 80.65.24.0 21
ip ip-prefix PL-ORION permit 81.25.76.0 22
ip ip-prefix PL-ORION permit 81.25.76.0 23
ip ip-prefix PL-ORION permit 81.25.76.0 24
ip ip-prefix PL-ORION permit 81.25.77.0 24
ip ip-prefix PL-ORION permit 81.25.78.0 23
ip ip-prefix PL-ORION permit 81.25.78.0 24
ip ip-prefix PL-ORION permit 81.25.79.0 24
ip ip-prefix PL-ORION permit 86.62.6.0 24
ip ip-prefix PL-ORION permit 94.73.192.0 18
ip ip-prefix PL-ORION permit 94.73.192.0 19
ip ip-prefix PL-ORION permit 94.73.202.0 24
ip ip-prefix PL-ORION permit 94.73.224.0 19
ip ip-prefix PL-ORION permit 94.73.240.0 20
ip ip-prefix PL-ORION permit 94.73.240.0 24
ip ip-prefix PL-ORION permit 94.73.241.0 24
ip ip-prefix PL-ORION permit 94.73.242.0 24
ip ip-prefix PL-ORION permit 94.73.243.0 24
ip ip-prefix PL-ORION permit 94.73.244.0 24
ip ip-prefix PL-ORION permit 94.73.245.0 24
ip ip-prefix PL-ORION permit 94.73.246.0 24
ip ip-prefix PL-ORION permit 94.73.247.0 24
ip ip-prefix PL-ORION permit 94.73.248.0 24
ip ip-prefix PL-ORION permit 94.73.249.0 24
ip ip-prefix PL-ORION permit 94.73.250.0 24
ip ip-prefix PL-ORION permit 94.73.251.0 24
ip ip-prefix PL-ORION permit 94.73.252.0 24
ip ip-prefix PL-ORION permit 109.226.192.0 18
ip ip-prefix PL-ORION permit 109.226.192.0 19
ip ip-prefix PL-ORION permit 109.226.224.0 19
ip ip-prefix PL-ORION permit 134.19.128.0 21
ip ip-prefix PL-ORION permit 171.33.248.0 21
ip ip-prefix PL-ORION permit 171.33.250.0 23
ip ip-prefix PL-ORION permit 171.33.252.0 23
ip ip-prefix PL-ORION permit 176.116.160.0 20
ip ip-prefix PL-ORION permit 176.116.160.0 21
ip ip-prefix PL-ORION permit 176.116.168.0 21
ip ip-prefix PL-ORION permit 185.84.172.0 23
ip ip-prefix PL-ORION permit 185.84.172.0 24
ip ip-prefix PL-ORION permit 185.84.173.0 24
```
</p>
</details>

### dig 

Программа для резолва DNS адресов

Пример резолва символического адреса с трассировкой 

<details><summary>dig +trace lenta.ru 80.65.16.1</summary>
<p>

```bash

Пример резолва адреса lenta.ru через DNS сервер 80.65.16.1

<details><summary>dig +trace lenta.ru 80.65.16.1</summary>
<p>

```bash
; <<>> DiG 9.11.5-P4-5.1+deb10u5-Debian <<>> +trace lenta.ru 80.65.16.1
;; global options: +cmd
.                       335512  IN      NS      l.root-servers.net.
.                       335512  IN      NS      j.root-servers.net.
.                       335512  IN      NS      m.root-servers.net.
.                       335512  IN      NS      f.root-servers.net.
.                       335512  IN      NS      h.root-servers.net.
.                       335512  IN      NS      g.root-servers.net.
.                       335512  IN      NS      a.root-servers.net.
.                       335512  IN      NS      d.root-servers.net.
.                       335512  IN      NS      c.root-servers.net.
.                       335512  IN      NS      b.root-servers.net.
.                       335512  IN      NS      i.root-servers.net.
.                       335512  IN      NS      e.root-servers.net.
.                       335512  IN      NS      k.root-servers.net.
.                       509232  IN      RRSIG   NS 8 0 518400 20210825050000 20210812040000 26838 . WrTvJW4fzqVP5/RTJkyowIZLXjuR9+IWtMhDwIEEEVaOHNNfPRo5pu9u f4OG1TicuWv+KU6orRNNaTBta3N/oAzPeVFu0cjbNRhCB9EI3nbFA2++ SYAEz2NrxZ7xJpnJ5oUxksvevOYe+SunFGO/Nl8j521Y+lQFRwyf0fRp MmIA+R2hyEu9LMZEl/EQHlg+s+PtIzuZIBpCWdJUN0L1hzgg1Aw5piUg YIqto+41PJ6b91nQqGw7ay2JVGP4LWBdQRr5qMB3xnPB7C9vmhXhpQIi cNYaAkYP2Gz+kS33N5WJ/XDGkAlmLCBJhKRGEyAxXV8bslgw0QggWmmV q6qKEg==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

ru.                     172800  IN      NS      a.dns.ripn.net.
ru.                     172800  IN      NS      b.dns.ripn.net.
ru.                     172800  IN      NS      d.dns.ripn.net.
ru.                     172800  IN      NS      e.dns.ripn.net.
ru.                     172800  IN      NS      f.dns.ripn.net.
ru.                     86400   IN      DS      7729 8 2 C035871113BB7C70C1FBFC1C9BFDFA9781733348D54F7EF9A4AD021A A00336F3
ru.                     86400   IN      RRSIG   DS 8 1 86400 20210825050000 20210812040000 26838 . nRXclAJcQi4vpoduZsGLMpFZo1XDgZhgoKnKbDH3HWiGTDcSAqmBwQVK clhJnntXwIp05lESc9+Ap7BSnjt8q4wydnZi5HjAW4pFOIckFdJVPGOc n/r6aANfdAF2z0w1mvC+sSrzS6aV62GYbL8j9eMdvebwP9LhZ0XxO45e 034dS/xEhEHbJ3DzIQgHtWpWLJ5pdjgamk3iyDhWQypc+qNcHH8JA+rR i0gGpxkgH2L3q4fx3N/w+6anrU1DSHXIgvXE8j9ra61abkc0+4VLldYL CaH/nxFhnTULNOoN4UTlDWyWiR3a+wWrpG1fIFoymUiKwOWIBzPFVTbq g7p/lQ==
;; Received 684 bytes from 199.7.91.13#53(d.root-servers.net) in 83 ms

lenta.ru.               345600  IN      NS      ns2.rambler.ru.
lenta.ru.               345600  IN      NS      ns3.rambler.ru.
lenta.ru.               345600  IN      NS      ns4.rambler.ru.
lenta.ru.               345600  IN      NS      ns5.rambler.ru.
tdui9d4jkuds8b9t86gj39pgflcnlgm5.ru. 3600 IN NSEC3 1 1 3 00FF TE33KGTAQU9M5CRQ3IBSILQU11GFGTTT NS SOA RRSIG DNSKEY NSEC3PARAM
tdui9d4jkuds8b9t86gj39pgflcnlgm5.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210905092702 20210731061836 19569 ru. EijlMV3UI2AWpv+n+mRzo/+DfY77anhN10Uyt2MKHCvduf8cxTrzSToU 6G309+HEMv356vI6IuBp+Dwlta1YjZ5XxlaV8PyJ8WNQaalqDqrUcT0b lk0lhVsWOCHq/alDfPEk2VLXw6i1+ixOwARWhEiqMiOS9gCuCM8ugNnU y+o=
cvdjk8qmto28dra6d6g6s2khntt6a6h9.ru. 3600 IN NSEC3 1 1 3 00FF CVLA5LV9LVJUN72AJD3Q3NNE28RPUHS1 NS DS RRSIG
cvdjk8qmto28dra6d6g6s2khntt6a6h9.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210903151641 20210725101810 19569 ru. kfmOLJJEvCofnf1VN4nzV79GMvt5c3s/UsKyBbWo0D6ktdLsixw3SuFV DNJCev30LVpHy8Ndj9WJ7gJZhepEUmAhtFvwi++TNLIx3spC3vcURHeL t970EZIz7f5LIZkInGpmehLYGppJ0hQbVKoEM6h8d3fF1vwKsbw6OilP iMs=
;; Received 724 bytes from 194.85.252.62#53(b.dns.ripn.net) in 11 ms

lenta.ru.               600     IN      A       81.19.72.32
lenta.ru.               600     IN      A       81.19.72.58
lenta.ru.               600     IN      A       81.19.72.56
lenta.ru.               600     IN      A       81.19.72.33
lenta.ru.               600     IN      A       81.19.72.57
lenta.ru.               600     IN      A       81.19.72.59
lenta.ru.               600     IN      NS      ns4.rambler.ru.
lenta.ru.               600     IN      NS      ns2.rambler.ru.
lenta.ru.               600     IN      NS      ns5.rambler.ru.
lenta.ru.               600     IN      NS      ns3.rambler.ru.
;; Received 333 bytes from 2a02:6b0:6:2::2:53#53(ns4.rambler.ru) in 55 ms

.                       335511  IN      NS      j.root-servers.net.
.                       335511  IN      NS      b.root-servers.net.
.                       335511  IN      NS      g.root-servers.net.
.                       335511  IN      NS      l.root-servers.net.
.                       335511  IN      NS      m.root-servers.net.
.                       335511  IN      NS      i.root-servers.net.
.                       335511  IN      NS      c.root-servers.net.
.                       335511  IN      NS      a.root-servers.net.
.                       335511  IN      NS      d.root-servers.net.
.                       335511  IN      NS      k.root-servers.net.
.                       335511  IN      NS      e.root-servers.net.
.                       335511  IN      NS      h.root-servers.net.
.                       335511  IN      NS      f.root-servers.net.
.                       509231  IN      RRSIG   NS 8 0 518400 20210825050000 20210812040000 26838 . WrTvJW4fzqVP5/RTJkyowIZLXjuR9+IWtMhDwIEEEVaOHNNfPRo5pu9u f4OG1TicuWv+KU6orRNNaTBta3N/oAzPeVFu0cjbNRhCB9EI3nbFA2++ SYAEz2NrxZ7xJpnJ5oUxksvevOYe+SunFGO/Nl8j521Y+lQFRwyf0fRp MmIA+R2hyEu9LMZEl/EQHlg+s+PtIzuZIBpCWdJUN0L1hzgg1Aw5piUg YIqto+41PJ6b91nQqGw7ay2JVGP4LWBdQRr5qMB3xnPB7C9vmhXhpQIi cNYaAkYP2Gz+kS33N5WJ/XDGkAlmLCBJhKRGEyAxXV8bslgw0QggWmmV q6qKEg==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

.                       86400   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2021081200 1800 900 604800 86400
.                       86400   IN      NSEC    aaa. NS SOA RRSIG NSEC DNSKEY
.                       86400   IN      RRSIG   SOA 8 0 86400 20210825050000 20210812040000 26838 . MVSk6dwXASaPCWDsGbpV4O3OfIUyruEf5p8IQ/KxKIOZjErjSPcxjTLI +lMpoWWclXT/iWSSDuIfxjfdlo52kMZG/2nm8iWs5tKEC9ut03Jy3Nnk 6SpDzd3cTTlAnfi+s/ENExIHcLTKj9qLplxVcUk25jHjuvAJhYl8Ti7g GP8WptJKV7L52sl2Xgs5I23UEpSArRPjzCqXeh9ZY3AtPk+p0IhRW4iI vV/KCCVzK/dRg+VdHoQyyaDWcInW7ZRM7I0Tv15o05j1xG1qL6ZwFeIi DNvvlqu5U8GRcSLDbHt/tz+YYAXdd0JrdIGjQ60P0bDmL3s43N+V+M39 kAIR7Q==
.                       86400   IN      RRSIG   NSEC 8 0 86400 20210825050000 20210812040000 26838 . dskPVWZdUnmOR2ioL3rjDal3whRdwqJOTw8Z4b1o0A3i1eNQB1S3lKsx 8OYNGPR0Xw5/sgddNlI//H6wffZJx0vvV15CSQVUViZfkO4wS72wknzz trpHPG7SHFYX2xMJ7RcIcaDISZxA1QPsAHZVdIOcD4JBmLpwbao5TwFP z7BrzR8Mk3aGpsWsEBzJuDm5Y8hIdN8u1U5bPQgVQT0wecXYDQb029dQ qK7dhu/Gl7PNJ8lTOuI4IxnSE6DHWTBVZ3CJga/OoY8jBgBFpZmOQRMR a0rBkhizln5RGpGb6fIgHTG1zomsY9s4PuhYPQc0keVoMZWvqmMSCuTC boK03A==
;; Received 711 bytes from 199.9.14.201#53(b.root-servers.net) in 94 ms

```
</p>
</details>

### ipcalc

Расчет ipv4 сетей

```bash
# посмотреть сетку без долбаного "dark blue" color
 ipcalc -n 109.226.192.0/18
```

### whois

Программа для работы с БД RIR

```bash
# узнать все маршруты, которые происходят из автономной системы.
whois -T route -i origin ASxxxxx 
```

Пример: список сетей закрепленных в RIPE за AS31257

<details><summary>whois -T route -i origin AS31257 | grep route</summary>
<p>

```bash
route:          109.226.192.0/18
route:          109.226.192.0/19
route:          109.226.224.0/19
route:          171.33.248.0/21
route:          171.33.250.0/23
route:          171.33.252.0/23
route:          176.116.160.0/20
route:          176.116.168.0/21
route:          185.84.172.0/23
route:          185.84.172.0/24
route:          80.65.16.0/20
route:          80.65.16.0/21
route:          80.65.24.0/21
route:          81.25.76.0/22
route:          81.25.76.0/23
route:          81.25.76.0/24
route:          81.25.77.0/24
route:          81.25.78.0/23
route:          81.25.78.0/24
route:          81.25.79.0/24
route:          86.62.6.0/24
route:          94.73.192.0/18
route:          94.73.192.0/19
route:          94.73.202.0/24
route:          94.73.224.0/19
route:          94.73.240.0/20
route:          94.73.240.0/24
route:          94.73.241.0/24
route:          94.73.242.0/24
route:          94.73.243.0/24
route:          94.73.244.0/24
route:          94.73.245.0/24
route:          94.73.246.0/24
route:          94.73.247.0/24
route:          94.73.248.0/24
route:          94.73.249.0/24
route:          94.73.250.0/24
route:          94.73.251.0/24
route:          94.73.252.0/24

```
</p>
</details>


```bash
# посмотреть шаблон объекта.
whois -t object_type 
```

Пример просмотра шаблона объекта "route"

<details><summary>whois -t route</summary>
<p>

```bash

% This is the RIPE Database query service.
% The objects are in RPSL format.
%
% The RIPE Database is subject to Terms and Conditions.
% See http://www.ripe.net/db/support/db-terms-conditions.pdf

route:          [mandatory]  [single]     [primary/lookup key]
descr:          [optional]   [multiple]   [ ]
origin:         [mandatory]  [single]     [primary/inverse key]
pingable:       [optional]   [multiple]   [ ]
ping-hdl:       [optional]   [multiple]   [inverse key]
holes:          [optional]   [multiple]   [ ]
org:            [optional]   [multiple]   [inverse key]
member-of:      [optional]   [multiple]   [inverse key]
inject:         [optional]   [multiple]   [ ]
aggr-mtd:       [optional]   [single]     [ ]
aggr-bndry:     [optional]   [single]     [ ]
export-comps:   [optional]   [single]     [ ]
components:     [optional]   [single]     [ ]
remarks:        [optional]   [multiple]   [ ]
notify:         [optional]   [multiple]   [inverse key]
mnt-lower:      [optional]   [multiple]   [inverse key]
mnt-routes:     [optional]   [multiple]   [inverse key]
mnt-by:         [mandatory]  [multiple]   [inverse key]
created:        [generated]  [single]     [ ]
last-modified:  [generated]  [single]     [ ]
source:         [mandatory]  [single]     [ ]

% This query was served by the RIPE Database Query Service version 1.101 (WAGYU)

```
</p>
</details>


```bash
"whois -i" - инверсный поиск. Ищет объекты у которых есть определеные поля.
```

[Inverse Queries](https://apps.db.ripe.net/docs/13.Types-of-Queries/05-Inverse-Queries.html)

```bash
All objects where contact person (or role) is XY-RIPE    	"-i person XY-RIPE"
All objects that are protected by XY-MNT	                "-i mnt-by XY-MNT"
All objects that have XY-MNT as mnt-lower	                "-i mnt-lower XY-MNT"
All objects that have XY-MNT as mnt-routes	                "-i mnt-routes XY-MNT"
All objects that have ORG-XYZ-RIPE as their organisation object	"-i org ORG-XYZ-RIPE"
All route/route6 objects that are originated by ASx	        "-i origin ASx"
```

Например:

"whois -i mnt-by NAUKANET-MNT | grep as-set"  - выведет список as-set для объектов,
у которых майнтейнером является NAUKANET-MNT

<details><summary>whois -i mnt-by NAUKANET-MNT | grep as-set</summary>
<p>

```bash
Например:
"whois -i mnt-by NAUKANET-MNT | grep as-set"  - выведет список as-set для объектов,
у которых майнтейнером является NAUKANET-MNT

Объяснение действий:
выведет все объекты БД RIPE, у которых есть mnt-by NAUKANET-MNT
+
grep по поля вхождения as-set

whois -i mnt-by NAUKANET-MNT | grep as-set
as-set:         AS-Don-plus
as-set:         AS-KONTAKT
as-set:         AS-NS-Luxoft
as-set:         AS-Nauka
as-set:         AS-NaukaCustomers
as-set:         AS-NaukaNet
as-set:         AS-NaukaNet6
as-set:         AS-NaukaV6
```
</p>
</details>


Далее уже можно смотреть содержимое непосредственно каждого объекта as-set:
<details><summary>whois AS-NaukaNet</summary>
<p>

```bash
% This is the RIPE Database query service.
% The objects are in RPSL format.
%
% The RIPE Database is subject to Terms and Conditions.
% See http://www.ripe.net/db/support/db-terms-conditions.pdf

% Note: this output has been filtered.
%       To receive output for a database update, use the "-B" flag.

% Information related to 'AS-NaukaNet'

as-set:         AS-NaukaNet
descr:          NaukaNet and customers
descr:          ********** Backbone AS *********
members:        AS8641
remarks:        =================================================
remarks:        ********** Global IP Transit Customers AS-SETs *********
remarks:        =================================================
members:        AS-NaukaCustomers
members:        AS-A2Customers
members:        AS-Sits
members:        AS-USACOV
members:        AS-ALFA-TELECOM
members:        AS-CITYCONNECT
members:        AS-FLEX
members:        AS-FTELECOM
members:        AS-GT
members:        AS-GT-IPV6
members:        AS-KOMDIV
members:        as-KOS-transit
members:        AS-LINENEW
members:        AS-LOFISNET
members:        AS-LOGIKA
members:        AS-Micron-Media
members:        AS-MOSINTER
members:        AS-NETORN
members:        AS-NEWAGE
members:        AS-RC
members:        AS-RINOKOM
members:        AS-SET-PlatinumOdin-NET
members:        AS-VILTEL
members:        AS-KONTAKT
members:        AS-Unittel
members:        AS-BIZTEL
members:        AS-SUN-TEL
members:        AS-ADMTEL
members:        AS-NORCOM
members:        AS-TEL-NET
members:        AS-EST
members:        AS-MOSLINE
members:        AS-LOGIX
members:        AS-STKTEL
members:        AS-BELRTS
members:        AS-BEGET
members:        AS-TOPTELECOM
members:        AS-TMPK
members:        AS-UTEX
members:        AS-SOUTHTEL
members:        AS-GPTEL
members:        AS-SPARKTELL
members:        AS-TransKom-VD
members:        AS-INN361
members:        AS-CDNNOW
members:        AS-EXPERTTELECOM
members:        AS-CSTNET
members:        AS-CGNET
members:        AS-SHARK
members:        AS-Summit-Systems
members:        AS-POZITIS-RU
members:        AS-FLYTELECOM
members:        AS-SSERVICE
members:        AS-ANT
members:        AS-TERATEL-SET
members:        AS-ASSIST24LTD
members:        AS-IMSYS
members:        AS-ODTV
members:        AS-ICEWOOD
members:        AS-MARYNONET
members:        AS-CHIP-TELECOM
members:        AS-OPCOM
members:        AS-PervajaBaza
members:        AS-BLIZCO16:11 $
last-modified:  2022-10-21T12:47:19Z
source:         RIPE # Filtered

role:           Nauka Telecom network NOC
address:        LLC Nauka Svyaz
address:        3-d Yamskogo Polya street, vladenie 2
address:        125124 Moscow Russia
phone:          +7 495 502 9092
fax-no:         +7 495 937 3412
nic-hdl:        NTnN1-RIPE
admin-c:        YG1510-RIPE
tech-c:         YG1510-RIPE
tech-c:         AB41990-RIPE
remarks:        +-----------------------------------------------------------
remarks:        ! Operation time:
remarks:        ! NOC: 5x8 (09:30-18:30) MSK
remarks:        ! customers support: 24x7
remarks:        +-----------------------------------------------------------
remarks:        ! Contacts:
remarks:        ! noc@naukanet.ru - for routing and peering questions
remarks:        ! abuse@naukanet.ru - for SPAM and abuse security issues
remarks:        ! helpdesk@naukanet.ru - for customers support
remarks:        ! sales@naukanet.ru - for sales questions
remarks:        ! http://www.naukanet.ru - official site
remarks:        +-----------------------------------------------------------
mnt-by:         NAUKANET-MNT
created:        2010-04-13T13:46:30Z
last-modified:  2021-06-04T09:18:42Z
source:         RIPE # Filtered

% This query was served by the RIPE Database Query Service version 1.104 (WAGYU)
```
</p>
</details>


```bash
#узнать все объекты, которые защищены объектом «maintainer_name».
whois -i mnt-by maintainer_name
```


```bash
#узнать, на какие более мелкие подсети поделена подсеть «subnet».
whois -M subnet 
```

Пример: узнать, на какие более мелкие подсети поделена подсеть 109.226.192.0/18

<details><summary>whois -M 109.226.192.0/18 | grep route</summary>
<p>

```bash


route:          109.226.192.0/19
route:          109.226.224.0/19
route:          109.226.247.0/24
route:          109.226.248.0/24
```
</p>
</details>


```bash
#узнать, из какой более крупной подсети образована подсеть «subnet».
whois -L subnet
```

Для того, чтобы узнай ip адреса dns серверов, которые закреплены за лукап реверсных зон можно использовать команду
```bash
"whois -xd 77.94.166.0/24 | grep nserver"

whois -xd 77.94.166.0/24 | grep nserver
nserver:        ns.naukanet.ru
nserver:        ns2.naukanet.ru
nserver:        ns3.naukanet.ru

```


Пример:  узнать, из какой более крупной подсети образована подсеть 109.226.192.0/19

<details><summary>whois -L 109.226.192.0/19 | grep route</summary>
<p>

```bash
route:          109.226.192.0/18
route:          109.226.192.0/19
```
</p>
</details>

Мониторинг трафика интерфейсов
```bash
jnettop
iftop
```

Мониторинг системы
```bash
top
htop
```

### iostat

[Анализ обмена данных на HDD iostat](https://itproffi.ru/komanda-iostat-analiz-obmena-dannymi-s-diskami/)
```bash
iostat
```

### fio

[Тест iops](https://winitpro.ru/index.php/2020/04/30/test-iops-diskov-v-linux/)
```bash
#Тест случайных операций на чтение/запись 
fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randwrite

#Тест случайных операций на чтение
fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=fiotest --filename=testfio --bs=4k --iodepth=64 --size=8G --readwrite=randread

```

### Мониторинг трафика
#### iftop
```bash
"iftop -i eth0"
```

#### jnettop
```bash
"jnettop -i eth0"
```

### Общие показатели системы
#### top
#### htop
#### atop


### Инвентаризация железа
https://webnote.satin-pl.com/2018/04/20/mon_hard_inventory_part3/

#### fdisk
#### lshw
#### lspci
#### dmidecode

### Навигация в Linux & etc...

#### cd 
#### ls
#### ll
#### mkdik
#### cat
#### tail
```bash
"tail -f"
```
#### multitail
#### wc
```bash
"cat <file> | wc -l" - показать количество строк в файле
```
#### touch fileXXX
#### rm
#### df
```bash
"df -h"
```
#### du
```bash
# Размер папок текущей директории
"du -h"

# Суммарный размер текущей директории
"du -sh"

#
"du -hd1"

```
#### pwd
#### whereis
#### find
#### locate
##### updatedb
#### diff
#### sort
#### grep
#### egrep
#### more
#### less
#### head
#### echo
#### &
#### &&
#### export
Environment variables are set when we open a new shell session.
#### file 
```bash
"file" - посмотреть тип файла

Пример использоания:

# бинарный файл
file /bin/tar
/bin/tar: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=6664753ff93e3b42aa8681d7fe0f7f9e5259c54f, stripped

# текстовый файл
file /etc/fstab
/etc/fstab: ASCII text

# блочный файл
file /dev/sda
/dev/sda: block special (8/0)

```

#### man 
manual of program

<details><summary>export</summary>
<p>

```bash
root@server# export
declare -x HOME="/root"
declare -x LANG="en_US.UTF-8"
declare -x LOGNAME="root"
declare -x LS_COLORS="rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:"
declare -x MAIL="/var/mail/root"
declare -x OLDPWD
declare -x PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
declare -x PWD="/home/manowar"
declare -x SHELL="/bin/bash"
declare -x SHLVL="1"
declare -x SUDO_COMMAND="/usr/bin/su"
declare -x SUDO_GID="1000"
declare -x SUDO_UID="1000"
declare -x SUDO_USER="manowar"
declare -x TERM="xterm"
declare -x USER="root"
```
</p>
</details>


### Работа с сервисами

#### service
```bash
"service" - deprecated, заменен на новых версиях на "systemclt" 
```
#### systemctl

### Пользователи/Права

Работа с пользователи/правами

#### adduser
#### useradd
#### addgroup
#### groupadd
#### groupmod
#### chmod
#### chown
#### passwd
#### chage
#### su 
#### sudo
#### who
#### last
#### id

#### Расширенные права facl
##### setfacl
```bash
"setfacl" - установить права
```
##### getfacl
```bash
"getfacl" - посмотреть права
```

### Работа с репозиториями
#### apt
#### atp-get
#### dpkg


### Работа с "экранами"
#### screen

#### tmux

[ШПАРГАЛКА ПО TMUX](https://losst.ru/shpargalka-po-tmux)
```bash
"tmux" - создать в новую сессию
"tmux new-session -s manowar" создать в новую сессию c именем manowar
"tmux ls" - показать список сессий
"tmux а" - присоединиться к сессии
"Ctrl-b w"  - переходить между текущим сессиями в tmux-e

"Ctrl-b Shift-5"  оно же "Ctrl-b %" -разделить текущую панель по горизонтали
"Ctrl-b + Shift-'" оно же "Ctrl-b " "  -разделить текущую панель по вертикале
"Ctrl-b стрелка"  - перейти на панель, находящуюся в стороне, куда указывает стрелка
"Ctrl-b d"  - отключиться от текущей сессии tmux-а
"Ctrl-d" - закрыть текущую панель в tmux-e
"Ctrl-b z"  - открыть текущую панель на весь экран

"Одновременный ввод во все панели"
"Ctrl+b :set synchronize-panes on" - синхронизировать ввод в панели  
        (одновременный ввод во всех панелях), 
        при вводе можно пользоваться табулятором при наборе "set synchronize-panes"
"Ctrl+b :set synchronize-panes off" - убрать синхронизацию ввода
        (убрать одновременный ввод во всех панелях)
        при вводе можно пользоваться табулятором при наборе "set synchronize-panes"
```


### Редакторы
#### vi
#### vim
#### nano
#### mcedit

### Управления сервисами
#### service
Подробности работы смотрите [здесь](https://icebale.readthedocs.io/en/latest/linux/base/#system_v)
```bash
"service" - программа, для управление "системой управления сервисами" - "System V"  

пример:
"service httpd status" - проверить статус сервиса httpd(apache2)
```

#### systemctl
Подробности работы смотрите [здесь](https://icebale.readthedocs.io/en/latest/linux/base/#systemd)
```bash
"systemctl" - программа, для управление "системой управления сервисами" - "Systemd"  

пример:
"systemctl status httpd" - проверить статус сервиса httpd(apache2)
```
#### logrotare
[баг unable to log rotate centos 7.4.1708 (Core)](https://forums.centos.org/viewtopic.php?t=69728)
```bash
#дебаг logrotate
/usr/sbin/logrotate -d /etc/logrotate.d/syslog
```

## Linux terminal hot key
```bash
clear - Ctrl-L
```
