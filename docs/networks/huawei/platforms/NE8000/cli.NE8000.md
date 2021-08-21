title: cli NE8000-F1A

# Базовые команды cli Huawei 
!!! note "Важно!"
		На маршрутизаторах Huawei используется система commit-а,
		что позволяет добавлять конфиг "логическо-консистентными" частями, 
		а также позволяет проверять набор изменений перед применением их в рабочий конфиг.

## Отобразить текущую конфигурацию
```bash
dis cur conf
```

## Просмотр конфига интерфейса
dis cur int XXX
```bash
dis cur int XGigabitEthernet 0/0/24
#
interface XGigabitEthernet0/0/24
 description << test >>
 port link-type trunk
 port trunk allow-pass vlan 10 20 to 25
 dhcp snooping trusted

```

## Перейти в режим редактирования конфигурации:
system-view

## посмотреть содержимое секции, в которой находишься сейчас
dis this
```bash
<NE8000-F1A>system-view
Enter system view, return user view with return command.
[~NE8000-F1A]int g0/1/3
[~NE8000-F1A-GigabitEthernet0/1/3]dis this
#
interface GigabitEthernet0/1/3
 mtu 9198
 description << to Uplink >>
 undo shutdown
 ip address 1.1.1.1 255.255.255.254
 traffic-policy TO-NATS inbound
 isis enable 1
 isis circuit-type p2p
 isis circuit-level level-2
 isis cost 100 level-2
 undo dcn
 statistic enable
#
return
```
## Commit/Rollback

### Commit

!!! note "Важно!"
    Commit [из режима конфигурирования]


#### Просмотр изменений до коммита
```bash
"display configuration candidate changes"

di co ca ch
```

#### Применить изменения 
```bash
Пример: закоммитить изменения с описанием 
"add xpl route-filter RP-TEST1" удобно указывать комментарий, 
чтобы потом понимать, что в данном commit-е происходило.

commit description add xpl route-filter RP-TEST1
```

#### Отменить непремененные изменения 
```bash
"clear configuration candidate"

c c c
```

### Просмотр изменений

#### Посмотреть список изменений с комментариями к ним 
```bash
"display configuration commit changes last ?"

di co co changes last ?
  1   CommitId 1000000232 created by admin at 2021-08-21 14:38:52+07:00
      (Description: xpl route-filter RP-TEST1)
  2   CommitId 1000000231 created by admin at 2021-08-21 14:34:05+07:00
```

#### Посмотреть какие изменения произошли в последнем коммита 
```bash
"display configuration commit changes last 1"

di co co ch last 1
```

#### Посмотреть какие изменения произошли в 3-ем коммите 
```bash
"display configuration commit changes last 3"

di co co ch last 3
```

### Rollback

!!! note "Важно!"
    Rollback(откат) <производится из операционного режима>

!!! note "Важно!"
    rollback применяется **СРАЗУ** без прохождения стадии commit-a
    что **НЕ ПОЗВОЛЯЕТ** проверять набор изменений перед применением их в рабочий конфиг.

!!! note "Важно понимать!!!"
    Rollback удаляет все промежуточные до выбранного коммита изменения 
    - что важно для сохранения консистентности конфигурации.

#### Откат к последней конфигурации
```bash
"rollback configuration last 1"

ro co la 1
```

#### Откат на 3 коммита назад
```bash
"rollback configuration last 3"

ro co la 3
```

## Routing

### показать RIB BGP для ip адреса 
**dis bgp routing-table <ip>**

```bash
<NE8000-F1A> dis bgp routing-table 1.1.1.1
 BGP local router ID : 10.10.10.10
 Local AS number : 31257
 Paths:   2 available, 1 best, 1 select, 0 best-external, 0 add-path
 BGP routing table entry information of 10.10.10.11/32:
 RR-client route.
 From: 2.2.2.2 (10.10.10.11)
 Route Duration: 1d05h07m34s
 Relay IP Nexthop: 2.2.2.2
 Relay IP Out-Interface: Eth-Trunk1008.3974
 Original nexthop: 2.2.2.2
 Qos information : 0x0
 AS-path Nil, origin incomplete, MED 1, localpref 100, pref-val 0, valid, internal, best, select, pre 255
 Advertised to such 3 peers:
    10.10.10.12
    10.10.10.13
    10.10.10.14

 BGP routing table entry information of 10.10.10.11/32:
 RR-client route.
 From: 10.10.10.11 (10.10.10.11)
 Route Duration: 1d13h22m16s
 Relay IP Nexthop: 10.10.10.11
 Relay IP Out-Interface: Eth-Trunk151
 Original nexthop: 10.10.10.11
 Qos information : 0x0
 AS-path Nil, origin incomplete, MED 1, localpref 100, pref-val 0, valid, internal, pre 255, IGP cost 100, not preferred for IGP cost
 Originator: 10.10.10.11
 Cluster list: 10.10.10.11
 Not advertised to any peer yet
```

### показать RIB для ip адреса 
**dis ip routing-table <ip>**

```bash
<NE8000-F1A>dis ip routing-table 1.1.1.1
Route Flags: R - relay, D - download to fib, T - to vpn-instance, B - black hole route
------------------------------------------------------------------------------
Routing Table : _public_
Summary Count : 1

Destination/Mask    Proto   Pre  Cost        Flags NextHop         Interface

  1.1.1.1/32  IBGP    255  1             RD  10.10.10.11  Eth-Trunk1000.3000
```
### показать FIB для определенного ip адреса
*cisco IOS аналог sh ip cef  <ip>*

*cisco NX-OS аналог show forward ip route <ip>*

**dis fib slot 1 <ip>**
```bash
<NE8000-F1A>dis fib slot 1 1.1.1
  Route Entry Count: 1
Destination/Mask   Nexthop         Flag TimeStamp     Interface                         TunnelID
1.1.1.1/32   10.10.10.11  DGU  16:46:43      Eth-Trunk1000.3000                0x0
```

## Посмотреть по регуляркам !!!
**display bgp routing-table regular-expression REG-EXP**

```bash
# Показать префиксы, которые начинаются (рождены) в AS50022 или AS65078
<NE8000-F1A>display bgp routing-table regular-expression .*(50022|65078)
 BGP Local router ID is 10.10.10.10
 Status codes: * - valid, > - best, d - damped, x - best external, a - add path,
               h - history,  i - internal, s - suppressed, S - Stale
               Origin : i - IGP, e - EGP, ? - incomplete
 RPKI validation codes: V - valid, I - invalid, N - not-found


 Total Number of Routes: 9
         Network            NextHop                       MED        LocPrf    PrefVal Path/Ogn

 *>i    10.10.10.0/24      10.10.31.254                    0          100        0      33991 65009 65027 65078i
 * i                       10.10.31.254                    0          100        0      33991 65009 65027 65078i
 * i                       10.10.31.254                    0          100        0      33991 65009 65027 65078i
 *>     172.10.0.0/16      10.10.31.32                                           0      50427 50022?
 * i                       10.10.31.34                                100        0      50427 50022?
```

## Редактирование xpl в редакторе vim
**edit xpl as-path-list APL-AMAZON**
