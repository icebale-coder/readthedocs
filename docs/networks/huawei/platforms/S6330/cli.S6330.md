title: cli S6330

# Базовые команды cli Huawei S6330
!!! warning "Важно!"
    Система commit-а отсутствует на коммутаторах Huawei, т.о. команды при редакировании конфига применяются сразу же

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

## Удаление влана из транка на интерфеса
В контексте интефейса

undo port trunk allow-pass vlan XXXX
```bash
#Задача удалить влан 2101 с интерфейса XGigabitEthernet 0/0/24
<DS-S6330>dis cur int XGigabitEthernet 0/0/24
  interface XGigabitEthernet0/0/24
   description << TEST >>
   port link-type trunk
   port trunk allow-pass vlan 2100 to 2101
   dhcp snooping trusted

<DS-S6330>system-view
[DS-S6330]int XGigabitEthernet 0/0/24
[DS-S6330-XGigabitEthernet0/0/24]undo port trunk allow-pass vlan 2101
Info: This operation may take a few seconds. Please wait a moment.done.

[DS-S6330]dis cur int XGigabitEthernet 0/0/24
#
  interface XGigabitEthernet0/0/24
   description << TEST >>
   port link-type trunk
   port trunk allow-pass vlan 2100
   dhcp snooping trusted
```

## Добавление влана в транк на интерфейс
В контексте интефейса

port trunk allow-pass vlan XXXX
```bash
#Здача добавить влан 2101 на интерфейс XGigabitEthernet 0/0/24
<DS-S6330>dis cur int XGigabitEthernet 0/0/24
  interface XGigabitEthernet0/0/24
   description << TEST >>
   port link-type trunk
   port trunk allow-pass vlan 2100 
   dhcp snooping trusted

<DS-S6330>system-view
[DS-S6330]int XGigabitEthernet 0/0/24
[DS-S6330-XGigabitEthernet0/0/24]port trunk allow-pass vlan 2101
Info: This operation may take a few seconds. Please wait a moment.done.

[DS-S6330]dis cur int XGigabitEthernet 0/0/24
#
  interface XGigabitEthernet0/0/24
   description << TEST >>
   port link-type trunk
   port trunk allow-pass vlan 2100 to 2101
   dhcp snooping trusted
```

## Просмотр информации по вланам
<details><summary>dis vlan</summary>
<p>

```bash  
<S6330>dis vlan
The total number of VLANs is: 5
--------------------------------------------------------------------------------
U: Up;         D: Down;         TG: Tagged;         UT: Untagged;
MP: Vlan-mapping;               ST: Vlan-stacking;
#: ProtocolTransparent-vlan;    *: Management-vlan;
--------------------------------------------------------------------------------

VID  Type    Ports
--------------------------------------------------------------------------------
1    common  UT:40GE0/0/1(D)    40GE0/0/2(D)    40GE0/0/3(D)    40GE0/0/4(D)
                40GE0/0/5(D)    40GE0/0/6(D)    XGE0/0/4(D)     XGE0/0/5(D)
                XGE0/0/6(D)     XGE0/0/7(D)     XGE0/0/8(D)     XGE0/0/9(D)
                XGE0/0/10(D)    XGE0/0/11(D)    XGE0/0/12(D)    XGE0/0/13(D)
                XGE0/0/14(D)    XGE0/0/15(D)    XGE0/0/16(D)    XGE0/0/17(D)
                XGE0/0/18(D)    XGE0/0/19(D)    XGE0/0/20(D)    XGE0/0/21(D)
                XGE0/0/22(D)    XGE0/0/23(D)    XGE0/0/24(U)
10   common  TG:XGE0/0/2(U)
1108 common  TG:XGE0/0/24(U)
2100 common  TG:XGE0/0/1(U)
2101 common  TG:XGE0/0/1(U)

VID  Status  Property      MAC-LRN Statistics Description
--------------------------------------------------------------------------------
1    enable  default       enable  disable    VLAN 0001
10   enable  default       enable  disable    VLAN 0010
1108 enable  default       enable  disable    VLAN 1108
2100 enable  default       enable  disable    VLAN 2100
2101 enable  default       enable  disable    VLAN 2101
```

</p>
</detail>


## Просмотреть текущий контекст (в котором находишься)
<details><summary>display this</summary>
<p>

```bash
<LAB-DS-S6330>system-view
Enter system view, return user view with Ctrl+Z.
[LAB-DS-S6330]int XGigabitEthernet 0/0/3
[LAB-DS-S6330-XGigabitEthernet0/0/3]display this
#
interface XGigabitEthernet0/0/3
 description -- UNUSED --
 port link-type trunk
 undo port trunk allow-pass vlan 1
#
return
```

</p>
</detail>


