title: EX cases

# Кейсы по EX

## Настройка L3 интерфейса на коммутаторе

```bash
"L3 интерфейс(SVI aka cisco) на ex коммутаторах, например на ex4500-40f"
"Создаем vlan 1000"
"Создаем L3 интерфейс в 1000-ом влане"
"Прописываем влан на интерфейсах"

    set vlans VL1000 description Test1000
    set vlans VL1000 vlan-id 1000
    set vlans VL1000 l3-interface vlan.1000

    set interfaces vlan unit 1000 description Test1000
    set interfaces vlan unit 1000 family inet address 172.16.164.211/29

    set interfaces xe-0/0/1 unit 0 family ethernet-switching vlan members VL1000
    set interfaces ae0 unit 0 family ethernet-switching vlan members VL1000
```

## Настройка LAG между EX-MX

```bash
  "MX> show configuration | display set | match ae0"         
    set interfaces xe-0/0/1 description "!L2 sw4500 xe-0/0/36; Part of ae0"
    set interfaces xe-0/0/1 gigether-options 802.3ad ae0
    set interfaces xe-1/0/1 description "!L2 sw4500 xe-0/0/37; Part of ae0"
    set interfaces xe-1/0/1 gigether-options 802.3ad ae0
    set interfaces xe-3/0/1 description "!L2 sw4500 xe-0/0/38; Part of ae0"
    set interfaces xe-3/0/1 gigether-options 802.3ad ae0
    set interfaces xe-4/0/4 description "!L2 sw4500 xe-0/0/39; Part of ae0"
    set interfaces xe-4/0/4 gigether-options 802.3ad ae0

    set interfaces ae0 description "!L2 sw4500_ae0 #LAG_40G#"
    set interfaces ae0 flexible-vlan-tagging
    set interfaces ae0 mtu 9216
    set interfaces ae0 encapsulation flexible-ethernet-services
    set interfaces ae0 aggregated-ether-options link-speed 10g
    set interfaces ae0 aggregated-ether-options lacp passive

  "EX-4500> show configuration | display set | match ae0"
    set interfaces xe-0/0/36 description "!UL2 MX_xe-0/0/1; Part of ae0"
    set interfaces xe-0/0/36 ether-options 802.3ad ae0
    set interfaces xe-0/0/37 description "!UL2 MX_xe-1/0/1; Part of ae0"
    set interfaces xe-0/0/37 ether-options 802.3ad ae0
    set interfaces xe-0/0/38 description "!UL2 MX_xe-3/0/1; Part of ae0"
    set interfaces xe-0/0/38 ether-options 802.3ad ae0
    set interfaces xe-0/0/39 description "!UL2 MX_xe-4/0/4; Part of ae0"
    set interfaces xe-0/0/39 ether-options 802.3ad ae0

    set interfaces ae0 description "!UL2 MX_ae9 #LAG_40G#"
    set interfaces ae0 mtu 9216
    set interfaces ae0 aggregated-ether-options link-speed 10g
    set interfaces ae0 aggregated-ether-options lacp active
    set interfaces ae0 unit 0 family ethernet-switching port-mode trunk
```
### Diagnostic

<details><summary> sw4500> show lacp interfaces ae0</summary>
<p>

```bash
"sw4500> show lacp interfaces ae0"       
  Aggregated interface: ae0
      LACP state:       Role   Exp   Def  Dist  Col  Syn  Aggr  Timeout  Activity
        xe-0/0/36      Actor    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-0/0/36    Partner    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-0/0/37      Actor    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-0/0/37    Partner    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-0/0/38      Actor    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-0/0/38    Partner    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-0/0/39      Actor    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-0/0/39    Partner    No    No   Yes  Yes  Yes   Yes     Fast   Passive
      LACP protocol:        Receive State  Transmit State          Mux State 
        xe-0/0/36                 Current   Fast periodic Collecting distributing
        xe-0/0/37                 Current   Fast periodic Collecting distributing
        xe-0/0/38                 Current   Fast periodic Collecting distributing
        xe-0/0/39                 Current   Fast periodic Collecting distributing
```
</p>
</details>

<details><summary> MX> show lacp interfaces ae0 extensive </summary>
<p>

```bash
"MX> show lacp interfaces ae0 extensive"
  Aggregated interface: ae0
      LACP state:       Role   Exp   Def  Dist  Col  Syn  Aggr  Timeout  Activity
        xe-1/0/1       Actor    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-1/0/1     Partner    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-2/0/1       Actor    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-2/0/1     Partner    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-3/0/1       Actor    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-3/0/1     Partner    No    No   Yes  Yes  Yes   Yes     Fast    Active
        xe-4/0/1       Actor    No    No   Yes  Yes  Yes   Yes     Fast   Passive
        xe-4/0/1     Partner    No    No   Yes  Yes  Yes   Yes     Fast    Active
      LACP protocol:        Receive State  Transmit State          Mux State 
        xe-1/0/1                  Current   Fast periodic Collecting distributing
        xe-2/0/1                  Current   Fast periodic Collecting distributing
        xe-3/0/1                  Current   Fast periodic Collecting distributing
        xe-4/0/1                  Current   Fast periodic Collecting distributing
      LACP info:        Role     System             System       Port     Port    Port 
                              priority         identifier   priority   number     key 
        xe-1/0/1       Actor        127  e8:a2:45:c7:7f:c4        127       34       8
        xe-1/0/1     Partner        127  78:fe:3d:d3:b6:80        127        1       1
        xe-2/0/1       Actor        127  e8:a2:45:c7:7f:c4        127       35       8
        xe-2/0/1     Partner        127  78:fe:3d:d3:b6:80        127        3       1
        xe-3/0/1       Actor        127  e8:a2:45:c7:7f:c4        127       36       8
        xe-3/0/1     Partner        127  78:fe:3d:d3:b6:80        127        2       1
        xe-4/0/1       Actor        127  e8:a2:45:c7:7f:c4        127       37       8
        xe-4/0/1     Partner        127  78:fe:3d:d3:b6:80        127        4       1
```
</p>
</details>



## ERPS 

ERPS (Ethernet Ring Protection Switching) - если по-простому, "своими словами", то это протокол L2 уровня, который позволяет создавать резервные пути для прохождения вланов без образование петель. 
По сути создается два пути по "кольцу" из коммутаторов, при чем один путь является основным, второй резервным.



Лит-ра:

[ERPS wikipedia](https://ru.wikipedia.org/wiki/ERPS#:~:text=ERPS%20(%D0%B0%D0%BD%D0%B3%D0%BB.,%D0%B1%D1%8B%D1%82%D1%8C%20%D0%B7%D0%B0%D0%BC%D0%B5%D0%BD%D0%BE%D0%B9%20%D1%81%D0%B5%D0%BC%D0%B5%D0%B9%D1%81%D1%82%D0%B2%D1%83%20%D0%BF%D1%80%D0%BE%D1%82%D0%BE%D0%BA%D0%BE%D0%BB%D0%BE%D0%B2%20STP)

[COMPUTRONIUM](http://1921680255.blogspot.com/2019/10/erps.html)

[Understanding Ethernet Ring Protection Switching Functionality](https://www.juniper.net/documentation/us/en/software/junos/high-availability/topics/concept/interfaces-understanding-ethernet-ring-protection-switching-functionality.html)