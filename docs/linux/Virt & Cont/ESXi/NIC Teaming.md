title: NIC Teaming (Объединение портов ESXi и ESX )
 
# Особенности работы сети в ESXi и ESX
https://bogachev.biz/2016/02/08/obedinenie-portov-esxi-i-esx/


## Режимы работы
Route based on the originating virtual switch port ID – распределение на основе идентификатора порта виртуального коммутатора. Этот механизм используется по умолчанию. Он выбирает для uplink-порт, на основании номера виртуального порта на виртуальном коммутаторе. Один виртуальный адаптер может одновременно использовать только один физический адаптер.
Данный механизм может обеспечивать равномерное распределение, если количество виртуальных сетевых адаптеров превышает количество физических адаптеров. Он не подходит для виртуальных серверов, обрабатывающих большое количество запросов от разных клиентов, когда необходимо разделять трафик одной виртуальной машины (с одним виртуальным сетевым адаптером) по нескольким NIC’ам. Этот механизм не позволяет использовать технологию агрегирования каналов 802.3ad. Кроме всего прочего проблемы могут возникнуть с доступом к IP-хранилищам (iSCSI, NFS), так как VMkernel аналогично сможет использовать только один физический NIC для работы с разными iSCSI-таргетами.
Route based on source MAC hash - распределение на основе хеширования MAC-адреса источника. Этот механизм аналогичен предыдущему и использовался по умолчанию в более ранних версиях ESX Server.
Route based on IP hash - распределение на основе хеширования IP-адреса источника и получателя. Трафик с одной виртуальной машины, направленный на разные IP-адреса, распределяется по разным физическим сетевым адаптерам. Для использования этого механизма необходимо включить поддержку 802.3ad на физическом коммутаторе, к которому подключен ESX Server.
Равномерность распределения трафика зависит от числа TCP/IP-сессий между хостом и различными клиентами. При большом количестве соединений Route based on IP hash позволяет распределять нагрузку наиболее равномерно и не имеет недостатков присущих механизму Route based on the originating virtual switch port ID.
Use explicit failover order - для передачи трафика используется первый по списку активный физический сетевой адаптер виртуального коммутатора, определенный параметром failover order, который определяет Active/Standby режим NIC’ов по отношению к виртуальному коммутатору.
Этот механизм удобно использовать на уровне портов виртуальных машин, сервисной консоли и VMkernel. Например, установив для сервисной консоли активный режим первого NIC’а и пассивный для второго, а для порта VMkernel соответственно наоборот, можно таким образом сконфигурировать failover для сетей управления.

### Пример:
<details><summary>Пример:</summary>
<p>

```bash
Пример:
!Сервера mysql на одном ESXi
      mysqls1 192.168.194.8
      mysqls2 192.168.194.9
      mysqls3 192.168.194.10
      mysqls4 192.168.194.11

      ESXi - физически подключен к разным Leaf-ам

      !<leaf1>dis arp vpn-instance VRF-MY dynamic | i 192.168.194.11
            ARP Entry Types: D - Dynamic, S - Static, I - Interface, O - OpenFlow, RD - Redirect
            EXP: Expire-time VLAN: VLAN or Bridge Domain

            IP ADDRESS      MAC ADDRESS    EXP(M) TYPE/VLAN       INTERFACE        VPN-INSTANCE
            ----------------------------------------------------------------------------------------
            192.168.194.11   0050-568e-d384    6   D/BD111         25GE1/0/10.111   VRF-MY
            ----------------------------------------------------------------------------------------
            Total:7557         Dynamic:9       Static:0    Interface:22    OpenFlow:0

      !<leaf2>dis arp vpn-instance VRF-MY dynamic | i 192.168.194.11
            ARP Entry Types: D - Dynamic, S - Static, I - Interface, O - OpenFlow, RD - Redirect
            EXP: Expire-time VLAN: VLAN or Bridge Domain

            IP ADDRESS      MAC ADDRESS    EXP(M) TYPE/VLAN       INTERFACE        VPN-INSTANCE
            ----------------------------------------------------------------------------------------
            192.168.194.11   0050-568e-d384        RD/BD111        192.168.50.56     VRF-MY
            ----------------------------------------------------------------------------------------
            Total:7557         Dynamic:8       Static:0    Interface:22    OpenFlow:0

      !<leaf1>display bgp vpnv4 vpn-instance VRF-MY peer                                             
            Status codes: * - Dynamic
            BGP local router ID : 192.168.33.37
            Local AS number : 65001

            VPN-Instance VRF-MY, Router ID 192.168.33.37:
            Total number of peers      : 4
            Peers in established state : 4
            Total number of dynamic peers : 4

            Peer                             V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State  PrefRcv
            *192.168.194.11                   4  65000   315420   367111     0 0481h52m Established        1
            *192.168.225.52                   4  65000   150465   151715     0 2191h39m Established        4
            *192.168.225.136                  4  65000   115117   116300     0 1679h22m Established        2
            *192.168.226.2                    4  65000   126769   128040     0 1848h21m Established        2      

      !<leaf2>display bgp vpnv4 vpn-instance VRF-MY peer
            Status codes: * - Dynamic
            BGP local router ID : 192.168.33.39
            Local AS number : 65001

            VPN-Instance VRF-MY, Router ID 192.168.33.39:
            Total number of peers      : 3
            Peers in established state : 3
            Total number of dynamic peers : 3

            Peer                             V          AS  MsgRcvd  MsgSent  OutQ  Up/Down       State  PrefRcv
            *192.168.194.8                    4  65000  1221122  1423061     0 1865h46m Established        1
            *192.168.194.9                    4  65000  1221120  1422922     0 1865h46m Established        1
            *192.168.194.10                   4  65000   315429   367556     0 0481h53m Established        1                                                                                              
```
</p>
</details>