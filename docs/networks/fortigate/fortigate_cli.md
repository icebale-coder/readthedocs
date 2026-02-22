title: Fortigate

## Работа с cli

### 0. Посмотреть модель оборудования
<details><summary>get hardware status</summary>
<p>

```bash
   get hardware status
    !firewall-1 #  get hardware status
     Model name: FortiGate-600D
     ASIC version: CP8
     ASIC SRAM: 64M
     CPU: Intel(R) Core(TM) i7-3770 CPU @ 3.40GHz
     Number of CPUs: 8
     RAM: 7995 MB
     Compact Flash: 15331 MB /dev/sda
     Hard disk: 114473 MB /dev/sdb
     USB Flash: not available
     Network Card chipset: Intel(R) Gigabit Ethernet Linux Driver (rev.0003)
     Network Card chipset: FortiASIC NP6 Adapter (rev.)                                                                                              
```
</p>
</details>

### 1. Посмотреть версию софта/модель
<details><summary>get system status</summary>
<p>

```bash
   !firewall-1 # get system status 
    Version: FortiGate-600D vX.X.X,build (GA)
    Virus-DB: 55.00000
    Extended DB: XX.XXXX
    Extreme DB: 
    IPS-DB: X.XXXXX
    IPS-ETDB: X.XXXXX
    APP-DB: X.XXXXX
    INDUSTRIAL-DB: X.XXXXX
    Serial-Number: FGT000000
    IPS Malicious URL Database: X.XXXXX
    BIOS version: 05000005
    System Part-Number: P0000
    Log hard disk: Available
    Hostname: firewall-1
    Private Encryption: Disable
    Operation Mode: NAT
    Current virtual domain: root
    Max number of virtual domains: 10
    Virtual domains status: 1 in NAT mode, 0 in TP mode
    Virtual domain configuration: disable
    FIPS-CC mode: disable
    Current HA mode: standalone
    Branch point: 1828
    Release Version Information: GA
    FortiOS x86-64: Yes
    System time: Thu Jul 24 18:24:17 2020                                                                                        
```
</p>
</details>

### 2. Просмотр таблицы маршрутизации
<details><summary>get router info routing-table all</summary>
<p>

```bash
     !firewall-1 # get router info routing-table all
      Codes: K - kernel, C - connected, S - static, R - RIP, B - BGP
          O - OSPF, IA - OSPF inter area
          N1 - OSPF NSSA ONE type 1, N2 - OSPF NSSA ONE type 2
          E1 - OSPF ONE type 1, E2 - OSPF ONE type 2
          i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
          * - candidate default

      Routing table for VRF=0
      B*      0.0.0.0/0 [20/0] via 100.100.100.59, ONE, 02:17:24
      B       10.0.0.0/8 [20/0] via 100.100.100.51, TWO, 02:17:24
      B       192.168.0.0/16 [20/0] via 100.100.100.51, TWO, 02:17:24
      S       1.1.1.216/29 [10/0] is a summary, Null
      C       100.100.100.48/29 is directly connected, TWO
      C       100.100.100.56/29 is directly connected, ONE
      C       192.168.255.254/32 is directly connected, GOOD
      C       192.168.255.255/32 is directly connected, BAD

      Routing table for VRF=30
      S*      0.0.0.0/0 [10/0] via 10.253.6.1, mgmt1
      C       10.253.6.0/24 is directly connected, mgmt1                                                                                
```
</p>
</details>

### 3. Просмотр NAT таблицы
<details><summary>get router info routing-table all</summary>
<p>

```bash
    !firewall-1 # get system session list 
     PROTO   EXPIRE SOURCE           SOURCE-NAT       DESTINATION      DESTINATION-NAT 
     udp     179    100.100.100.57:1492 -                200.200.200.53:53 -               
     udp     179    100.100.100.57:1492 -                200.200.200.52:53 -               
     tcp     3591   100.100.100.49:15520 -                100.100.100.51:179 -               
     icmp    45     192.168.1.104:46179 1.1.1.217:46179 8.8.8.8:8        -                                                                                           
```
</p>
</details>

### 4. Просмотр конфигурации
<details><summary>show</summary>
<p>

```bash
   show
    show | grep xxxx
    show full-configuration
    show full-configuration | grep XXXX
    show full-configuration | grep -f XXXX ← display with tree view


   !Пример показа вхождения "Microsoft Office 365" чз grep 
   !firewall-1 # show firewall addrgrp 
    config firewall addrgrp
        edit "Microsoft Office 365"
            set uuid XXXXXX1-YYYYYY1-ZZZZZ1
            set member "login.microsoftonline.com" "login.microsoft.com" "login.windows.net"
        next
        edit "DOCKER"
            set uuid XXXXXX-YYYYYY-ZZZZZ
            set member "192.168.32.0/19" "192.168.64.0/19"
        next
    end

   !firewall-1 # show  | grep -f "Microsoft Office 365"
    config firewall addrgrp
        edit "Microsoft Office 365" <---
            set uuid XXXXXX1-YYYYYY1-ZZZZZ1
            set member "login.microsoftonline.com" "login.microsoft.com" "login.windows.net"
        next
    end    

   !аналогично вывода секции "Microsoft Office 365"
   !firewall-1 # show firewall addrgrp "Microsoft Office 365"
    config firewall addrgrp
        edit "Microsoft Office 365"
            set uuid XXXXXX1-YYYYYY1-ZZZZZ1
            set member "login.microsoftonline.com" "login.microsoft.com" "login.windows.net"
        next
    end       

   !Пример вхождения в секции "192.168.32.0/19"
   !firewall-1 # show  | grep -f "192.168.32.0/19"
    config firewall address
        edit "192.168.32.0/19" <---
            set uuid XXXXXX2-YYYYYY2-ZZZZZ2
            set subnet 192.168.32.0 255.255.224.0
        next
    end
    config firewall addrgrp
        edit "DOCKER"
            set uuid XXXXXX-YYYYYY-ZZZZZ
            set member "192.168.32.0/19" "192.168.64.0/19"  <---
        next
    end
```
</p>
</details>

### 5. Редактирование конфигурации
<details><summary>get router info routing-table all</summary>
<p>

```bash

   Добавить ip адресов "2.2.2.2/32" и "3.3.3.3/32" в группу "DOCKER"
    config firewall address
        edit "2.2.2.2/32"
            set subnet 2.2.2.2 255.255.255.255
        next
        edit "3.3.3.3/32"
            set subnet 3.3.3.3 255.255.255.255
        next    
    end

    config firewall addrgrp
        edit "DOCKER"
            append member "2.2.2.2/32"
            append member "3.3.3.3/32"
        next
    end

   Просмотр изменений в группе "DOCKER"
    !firewall-1 # show firewall addrgrp DOCKER
     config firewall addrgrp
         edit "DOCKER"
             set uuid XXXXXX-YYYYYY-ZZZZZ
             set member "192.168.32.0/19" "192.168.64.0/19"
         next
     end                                                                                           
```
</p>
</details>

### 6. Просмотр bgp
<details><summary>get router info bgp sum</summary>
<p>

```bash

   !firewall-1 # get router info bgp sum
    VRF 0 BGP router identifier 10.100.63.6, local AS number 4200508201
    BGP table version is 3
    4 BGP AS-PATH entries
    0 BGP community entries

    Neighbor      V         AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
    100.100.100.51 4 4200508101     254     244        1    0    0 03:34:27        2
    100.100.100.52 4 4200508102       0       0        0    0    0    never Active     
    100.100.100.59 4 4200508101     255     252        2    0    0 03:34:27        1
    100.100.100.60 4 4200508102       0       0        0    0    0    never Active     

    Total number of neighbors 4


   !firewall-1 # get router info bgp neighbors 100.100.100.51 routes
    VRF 0 BGP table version is 3, local router ID is 10.100.63.6
    Status codes: s suppressed, d damped, h history, * valid, > best, i - TWO,
                  S Stale
    Origin codes: i - IGP, e - EGP, ? - incomplete

       Network          Next Hop            Metric LocPrf Weight RouteTag Path
    *> 10.0.0.0         100.100.100.51            0             0        0 4200508101 4200508901 ? <-/1>
    *> 192.168.0.0/16    100.100.100.51            0             0        0 4200508101 4200508901 4200511000 ? <-/1>

    Total number of prefixes 2



   !firewall-1 # get router info bgp neighbors 100.100.100.51 advertised-routes
    VRF 0 BGP table version is 3, local router ID is 10.100.63.6
    Status codes: s suppressed, d damped, h history, * valid, > best, i - TWO
    Origin codes: i - IGP, e - EGP, ? - incomplete

       Network          Next Hop            Metric LocPrf Weight RouteTag Path
    *> 192.168.255.254/32
                        100.100.100.49                 100  32768        0 i <-/->
    *> 192.168.255.255/32
                        100.100.100.49                 100  32768        0 i <-/->

    Total number of prefixes 2                                                                             
```
</p>
</details>


### 7. Настройка bfd в BGP
<details><summary>get router info bgp sum</summary>
<p>

```bash
   7.1. на bgp neighbour 
     !firewall-1 
      config router bgp
          config neighbor
              edit "100.100.100.51"
                  set bfd enable
   7.2. На инте 
     !firewall-1 
      config system interface
          edit "INTERTAL"
              set bfd enable
      end

      config system interface
          edit "ONE"
              set bfd enable
      end                                                                      
```
</p>
</details>

### 8. Просмотр bfd
<details><summary>get router info bfd neighbor </summary>
<p>

```bash
   !firewall-1 # get router info bfd neighbor 
    OurAddress      NeighAddress    State       Interface       LDesc/RDesc
    100.100.100.49   100.100.100.51   UP          TWO        2/19
    100.100.100.57   100.100.100.59   UP          ONE        1/18      

   !firewall-1 # get router info bfd neighbor detail
    OurAddress      NeighAddress    State       Interface       LDesc/RDesc
    100.100.100.49   100.100.100.51   UP          TWO        2/19
    Local Diag: 0, Demand mode: no, Poll bit: unset
    MinTxInt: 250, MinRxInt: 250, Multiplier: 3
    Received: MinRxInt: 300 (ms), MinTxInt: 300 (ms),Multiplier: 3
    Transmit Interval: 300 (ms), Detection Time: 900 (ms)
    Rx Count: 44201, Rx Interval (ms) min/max/avg 0/1100/290 last 230 (ms) ago
    Tx Count: 50556, Tx Interval (ms) min/max/avg 70/1950/220  last: 0 (ms) ago
    Registered protocols: BGP

    OurAddress      NeighAddress    State       Interface       LDesc/RDesc
    100.100.100.57   100.100.100.59   UP          ONE        1/18
    Local Diag: 0, Demand mode: no, Poll bit: unset
    MinTxInt: 250, MinRxInt: 250, Multiplier: 3
    Received: MinRxInt: 300 (ms), MinTxInt: 300 (ms),Multiplier: 3
    Transmit Interval: 300 (ms), Detection Time: 900 (ms)
    Rx Count: 44195, Rx Interval (ms) min/max/avg 0/2900/290 last 10 (ms) ago
    Tx Count: 50572, Tx Interval (ms) min/max/avg 80/2160/220  last: 110 (ms) ago
    Registered protocols: BGP                                                             
```
</p>
</details>