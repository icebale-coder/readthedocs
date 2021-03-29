title: Huawei cli base S6330

# Базовые команды cli Huawei S6330
!!! warning "Важно!"
    Система commit-а отсутствует на коммутаторах Huawei
    Т.о. команды при редакировании конфига применяются сразу же

## Просмотр конфига

```bash
dis cur conf
```

## Просмотр конфига интерфейса

```bash
<S6330>dis cur int XGigabitEthernet 0/0/24
#
interface XGigabitEthernet0/0/24
 description << test >>
 port link-type trunk
 port trunk allow-pass vlan 10 20 to 25
 dhcp snooping trusted

```

## Просмотр текущего состояния интерфейса

<details><summary>dis int XGigabitEthernet 0/0/24</summary>
<p>

```bash
XGigabitEthernet0/0/24 current state : UP
Line protocol current state : UP
Description:<< LAB-DS-C4900M Te1/8 [10G] >>
Switch Port, Link-type : trunk(configured),
PVID :    1, TPID : 8100(Hex), The Maximum Frame Length is 9216
IP Sending Frames' Format is PKTFMT_ETHNT_2, Hardware address is a400-e27a-99f0
Last physical up time   : 2021-03-03 13:49:10 UTC+07:00
Last physical down time : 2021-03-02 09:35:24 UTC+07:00
Current system time: 2021-03-29 12:46:00+07:00
Port Mode: COMMON FIBER, Transceiver: UNKNOWN_SFP
Speed : 10000,  Loopback: NONE
Duplex: FULL,   Negotiation: DISABLE
Mdi   : -,      Flow-control: DISABLE
Last 300 seconds input rate 24432 bits/sec, 32 packets/sec
Last 300 seconds output rate 23792 bits/sec, 31 packets/sec
Input peak rate 421849296 bits/sec, Record time: 2021-03-26 17:54:35
Output peak rate 101307664 bits/sec, Record time: 2021-03-01 13:49:48

Input:  106345387 packets, 15725199006 bytes
  Unicast:                  104412883,  Multicast:                     1924712
  Broadcast:                     1272,  Jumbo:                         3305774
  Discard:                          0,  Pause:                               0

  Total Error:                   6520
  CRC:                              0,  Giants:                           6520
  Runts:                            0,  Fragments:                           0
  Alignments:                       0,  Symbols:                             0
  Ignoreds:                         0

Output:  99111904 packets, 9541883968 bytes
  Unicast:                   97278830,  Multicast:                     1182402
  Broadcast:                   650672,  Jumbo:                           13806
  Discard:                          0,  Pause:                               0


    Input bandwidth utilization threshold : 80.00%
    Output bandwidth utilization threshold: 80.00%
    Input bandwidth utilization  :    0%
    Output bandwidth utilization :    0%
```    

</p>
</details>


## Пример редактирования настроек интерфейса 

```bash

# Просмотр настроек итерфейса перед изменением настроек
<S6330>dis cur int XGigabitEthernet 0/0/24
interface XGigabitEthernet0/0/24
 description << test >>
 port link-type trunk
 port trunk allow-pass vlan 1108 2100 to 2101
 dhcp snooping trusted

# Переход в режим конфигурирования
<S6330>system-view
Enter system view, return user view with Ctrl+Z.
[S6330]

# Переход в контекст редактирования настроек интерфейса
[S6330]interface XGigabitEthernet 0/0/24

# Редактирование настроек интерфейса
[S6330-XGigabitEthernet0/0/24]undo port trunk allow-pass vlan 2100 to 2101
Info: This operation may take a few seconds. Please wait a moment.done.

# Просмотр настроек итерфейса перед изменением настроек
[s6330]dis cur int XGigabitEthernet 0/0/24

interface XGigabitEthernet0/0/24
 description << test >>
 port link-type trunk
 port trunk allow-pass vlan 1108
 dhcp snooping trusted
```