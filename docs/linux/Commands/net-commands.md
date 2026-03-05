title: Network commands


# Network commands
Disclaimer:
```bash
Всё что есть в сетевых вендорах не более чем форк *nix...
```

!!! note "JFYI"
		В данном разделе будет отчасти дублироваться команды из раздела "Linux commands", 
        чтобы сгруппировать команды по работе с сетью в одном месте.


## Команды работы с интефейсами

### 1. Просмотр состояния интерфейсов 
!!! note "NB"
```bash
    команды
    ifconfig          - depricated
    ip (из iproute2)  - current
``` 


#### 1.1. Просмотр состояния всех интерфейсов
<details><summary>ifconfig -a </summary>
<p>

```bash
    [NB] без "-a" - не покажет неактивные интерфейсы
    r1:/# ifconfig -a
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
              inet addr:172.20.20.3  Bcast:172.20.20.255  Mask:255.255.255.0
              BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:742 errors:0 dropped:0 overruns:0 frame:0
              TX packets:157 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:53492 (52.2 KiB)  TX bytes:11302 (11.0 KiB)

    lo        Link encap:Local Loopback  
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000 
              RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)                                                                                            
```
</p>
</details>

<details><summary>ip a </summary>
<p>

```bash
    r1:/# ip a 
     1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
         link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
         inet 127.0.0.1/8 scope host lo
            valid_lft forever preferred_lft forever
         inet6 ::1/128 scope host proto kernel_lo 
            valid_lft forever preferred_lft forever
     7: eth0@if8: <BROADCAST,MULTICAST> mtu 1500 qdisc noqueue state DOWN group default 
         link/ether 02:42:ac:14:14:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 172.20.20.3/24 brd 172.20.20.255 scope global eth0
            valid_lft forever preferred_lft forever                                                                                         
```
</p>
</details>


#### 1.2. Просмотр состояния конкретного интерфейсов
<details><summary>ipconfig eth0</summary>
<p>

```bash
    r1:/# ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
              inet addr:172.20.20.3  Bcast:172.20.20.255  Mask:255.255.255.0
              BROADCAST MULTICAST  MTU:1500  Metric:1
              RX packets:746 errors:0 dropped:0 overruns:0 frame:0
              TX packets:173 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:54012 (52.7 KiB)  TX bytes:12614 (12.3 KiB)                                                                                          
```
</p>
</details>

<details><summary>ip a show eth0</summary>
<p>

```bash
    r1:/# ip a show eth0
     7: eth0@if8: <BROADCAST,MULTICAST> mtu 1500 qdisc noqueue state DOWN group default 
        link/ether 02:42:ac:14:14:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 172.20.20.3/24 brd 172.20.20.255 scope global eth0
           valid_lft forever preferred_lft forever                                                                                      
```
</p>
</details>

### 2. Включение/Выключение интерфейса  
#### 2.1. Включение интерфейса
<details><summary>ifconfig eth0 up</summary>
<p>

```bash
     r1:/# ifconfig eth0 up
     r1:/# ifconfig eth0
     eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
               inet addr:172.20.20.3  Bcast:172.20.20.255  Mask:255.255.255.0
               inet6 addr: fe80::42:acff:fe14:1403/64 Scope:Link
               UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
               RX packets:746 errors:0 dropped:0 overruns:0 frame:0
               TX packets:170 errors:0 dropped:0 overruns:0 carrier:0
               collisions:0 txqueuelen:0 
               RX bytes:54012 (52.7 KiB)  TX bytes:12404 (12.1 KiB)
                                                                                     
```
</p>
</details>

<details><summary>ip link set eth0 up</summary>
<p>

```bash
    r1:/# ip link set eth0 up
    r1:/# ip a show eth0
    7: eth0@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:ac:14:14:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 172.20.20.3/24 brd 172.20.20.255 scope global eth0
           valid_lft forever preferred_lft forever
        inet6 fe80::42:acff:fe14:1403/64 scope link proto kernel_ll 
           valid_lft forever preferred_lft forever                                                                       
```
</p>
</details>

#### 2.2. Выключение интерфейса
<details><summary>ifconfig eth0 down</summary>
<p>

```bash
     r1:/# ifconfig eth0 down
     r1:/# ifconfig eth0
     eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
               inet addr:172.20.20.3  Bcast:172.20.20.255  Mask:255.255.255.0
               BROADCAST MULTICAST  MTU:1500  Metric:1
               RX packets:750 errors:0 dropped:0 overruns:0 frame:0
               TX packets:191 errors:0 dropped:0 overruns:0 carrier:0
               collisions:0 txqueuelen:0 
               RX bytes:54532 (53.2 KiB)  TX bytes:14066 (13.7 KiB)
                                                                                     
```
</p>
</details>

<details><summary>ip link set eth0 down </summary>
<p>

```bash
     r1:/# ip link set eth0 down
     r1:/# ip a show eth0
     7: eth0@if8: <BROADCAST,MULTICAST> mtu 1500 qdisc noqueue state DOWN group default 
         link/ether 02:42:ac:14:14:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 172.20.20.3/24 brd 172.20.20.255 scope global eth0
            valid_lft forever preferred_lft forever                                                                      
```
</p>
</details>


### 3. Доваление/Удаление ip адреса ipv4/ipv6
#### 3.1. Добавление ipv4
<details><summary>ifconfig eth0 192.168.1.2 netmask 255.255.255.0</summary>
<p>

```bash
     [NB] ifconfig - не может напрямую добавить адрес, а может только заменить существующий 

     r1:/# ifconfig eth0
      eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
                inet addr:192.168.1.1  Bcast:192.168.1.255  Mask:255.255.255.0
                inet6 addr: fe80::42:acff:fe14:1403/64 Scope:Link
                UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                RX packets:755 errors:0 dropped:0 overruns:0 frame:0
                TX packets:226 errors:0 dropped:0 overruns:0 carrier:0
                collisions:0 txqueuelen:0 
                RX bytes:55138 (53.8 KiB)  TX bytes:17036 (16.6 KiB)

      r1:/# ifconfig eth0 192.168.1.2 netmask 255.255.255.0
      r1:/# ifconfig eth0
      eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
                inet addr:192.168.1.2  Bcast:192.168.1.255  Mask:255.255.255.0
                inet6 addr: fe80::42:acff:fe14:1403/64 Scope:Link
                UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                RX packets:755 errors:0 dropped:0 overruns:0 frame:0
                TX packets:226 errors:0 dropped:0 overruns:0 carrier:0
                collisions:0 txqueuelen:0 
                RX bytes:55138 (53.8 KiB)  TX bytes:17036 (16.6 KiB)      

     [NB] Для добавление второго адреса надо использовать alias(виртуальный интерфейс)           
       ifconfig eth0:0 192.168.1.10 netmask 255.255.255.0 up
        r1:/# ifconfig eth0:0 192.168.1.10 netmask 255.255.255.0 up

        "И выглядет это стрёмно..." 
        r1:/# ifconfig
        eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
                  inet addr:192.168.1.2  Bcast:192.168.1.255  Mask:255.255.255.0
                  inet6 addr: fe80::42:acff:fe14:1403/64 Scope:Link
                  UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                  RX packets:756 errors:0 dropped:0 overruns:0 frame:0
                  TX packets:227 errors:0 dropped:0 overruns:0 carrier:0
                  collisions:0 txqueuelen:0 
                  RX bytes:55208 (53.9 KiB)  TX bytes:17106 (16.7 KiB)

        eth0:0    Link encap:Ethernet  HWaddr 02:42:AC:14:14:03  
                  inet addr:192.168.1.10  Bcast:192.168.1.255  Mask:255.255.255.0
                  UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1

        lo        Link encap:Local Loopback  
                  inet addr:127.0.0.1  Mask:255.0.0.0
                  inet6 addr: ::1/128 Scope:Host
                  UP LOOPBACK RUNNING  MTU:65536  Metric:1
                  RX packets:156 errors:0 dropped:0 overruns:0 frame:0
                  TX packets:156 errors:0 dropped:0 overruns:0 carrier:0
                  collisions:0 txqueuelen:1000 
                  RX bytes:9516 (9.2 KiB)  TX bytes:9516 (9.2 KiB)                                                                                     
```
</p>
</details>


<details><summary>ip a a 192.168.1.2/24 dev eth0</summary>
<p>

```bash
     [NB] ip - добавиляет новый адрес на интерфейс, а для того чтобы поменять сущекствующий надо удалить предыдущий.  

      r1:/# ip a show eth0
      22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
          link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
          inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
             valid_lft forever preferred_lft forever
          inet6 3fff:172:20:20::4/64 scope global nodad 
             valid_lft forever preferred_lft forever
          inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
             valid_lft forever preferred_lft forever
      r1:/# ip a a 192.168.1.2/24 dev eth0
      r1:/# ip a show eth0
      22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
          link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
          inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
             valid_lft forever preferred_lft forever
          inet 192.168.1.2/24 scope global secondary eth0
             valid_lft forever preferred_lft forever
          inet6 3fff:172:20:20::4/64 scope global nodad 
             valid_lft forever preferred_lft forever
          inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
             valid_lft forever preferred_lft forever                                                                               
```
</p>
</details>

#### 3.2. Добавление ipv6
!!! note "NB" 
    для ifconfig затруднительно добавление ipv6, поэтому приведу только для команды "ip"

<details><summary>ip a a 2001:0db8:1234:5678::1/64 dev eth0</summary>
<p>

```bash
     r1:/# ip a a 2001:0db8:1234:5678::1/64 dev eth0
     r1:/# ip a show eth0
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
         link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
            valid_lft forever preferred_lft forever
         inet6 2001:db8:1234:5678::1/64 scope global 
            valid_lft forever preferred_lft forever
         inet6 3fff:172:20:20::4/64 scope global nodad 
            valid_lft forever preferred_lft forever
         inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
            valid_lft forever preferred_lft forever                                                                                 
```
</p>
</details>


#### 3.3. Удаление ipv4
!!! note "NB" 
    для ifconfig можно только поменять ipv4 адрес, вместо этого пользуемся командой "ip"

<details><summary>ip a d 192.168.1.2/24 dev eth0</summary>
<p>

```bash
     r1:/# ip a a 2001:0db8:1234:5678::1/64 dev eth0
     r1:/# ip a show eth0
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
         link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
            valid_lft forever preferred_lft forever

      r1:/# ip a d 192.168.1.2/24 dev eth0

      r1:/# ip a show eth0
      22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
          link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
          inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
             valid_lft forever preferred_lft forever                                                                              
```
</p>
</details>

#### 3.4. Удаление ipv6
!!! note "NB" 
    ддля ifconfig затруднительно удаление ipv6, поэтому приведу только для команды "ip"

<details><summary>ip a d 2001:0db8:1234:5678::1/64 dev eth0</summary>
<p>

```bash
     r1:/# ip a show eth0
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
         link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
            valid_lft forever preferred_lft forever
         inet6 2001:db8:1234:5678::1/64 scope global 
            valid_lft forever preferred_lft forever
         inet6 3fff:172:20:20::4/64 scope global nodad 
            valid_lft forever preferred_lft forever
         inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
            valid_lft forever preferred_lft forever

     r1:/# ip a d 2001:0db8:1234:5678::1/64 dev eth0

     r1:/# ip a show eth0
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
         link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
         inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
            valid_lft forever preferred_lft forever
         inet6 3fff:172:20:20::4/64 scope global nodad 
            valid_lft forever preferred_lft forever
         inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
            valid_lft forever preferred_lft forever                                                                        
```
</p>
</details>

### 4. Смена mac адреса

<details><summary>ifconfig eth0 hw ether 00:11:22:33:44:55</summary>
<p>

```bash
    r1:/# ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 02:42:AC:14:14:04  
              inet addr:192.168.1.1  Bcast:192.168.1.255  Mask:255.255.255.0
              inet6 addr: 3fff:172:20:20::4/64 Scope:Global
              inet6 addr: fe80::42:acff:fe14:1404/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:70 errors:0 dropped:0 overruns:0 frame:0
              TX packets:22 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0 
              RX bytes:6600 (6.4 KiB)  TX bytes:2884 (2.8 KiB)

    r1:/# ifconfig eth0 hw ether 00:11:22:33:44:55
    r1:/# ifconfig eth0
    eth0      Link encap:Ethernet  HWaddr 00:11:22:33:44:55  
              inet addr:192.168.1.1  Bcast:192.168.1.255  Mask:255.255.255.0
              inet6 addr: 3fff:172:20:20::4/64 Scope:Global
              inet6 addr: fe80::42:acff:fe14:1404/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:70 errors:0 dropped:0 overruns:0 frame:0
              TX packets:22 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0                                                                     
```
</p>
</details>

<details><summary>ip link set dev eth0 address 00:11:22:33:44:55</summary>
<p>

```bash
    r1:/# ip a show eth0
    22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:ac:14:14:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
           valid_lft forever preferred_lft forever
        inet6 3fff:172:20:20::4/64 scope global nodad 
           valid_lft forever preferred_lft forever
        inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
           valid_lft forever preferred_lft forever
    r1:/# ip link set dev eth0 address 00:11:22:33:44:55
    r1:/# ip a show eth0
    22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
           valid_lft forever preferred_lft forever
        inet6 3fff:172:20:20::4/64 scope global nodad 
           valid_lft forever preferred_lft forever
        inet6 fe80::42:acff:fe14:1404/64 scope link proto kernel_ll 
           valid_lft forever preferred_lft forever                                                               
```
</p>
</details>

### 5. Работа с VRF
#### 5.1. Создание VRF

<details><summary>ip link add VRF_RED type vrf table 1001</summary>
<p>

</p>
</details>


<details><summary>ip link set dev VRF_RED up</summary>
<p>

```bash
    r1:/# ip link add VRF_RED type vrf table 1001
    r1:/# ip vrf show
    Name              Table
    -----------------------
    VRF_RED           1001                                                             
```
</p>
</details>

#### 5.2. Добавление интерфейса в VRF

<details><summary>ip link set dev eth0 master VRF_RED</summary>
<p>

</p>
</details>

<details><summary>ip link set dev eth1 master VRF_RED</summary>
<p>

```bash
    r1:/# ip a show eth0
    22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master default state UP group default 
        link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 192.168.1.1/24 brd 192.168.1.255 scope global eth0
           valid_lft forever preferred_lft forever
        inet6 fe80::211:22ff:fe33:4455/64 scope link proto kernel_ll 
           valid_lft forever preferred_lft forever

    r1:/# ip a show eth1
    37: eth1@if36: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 qdisc noqueue master VRF_RED state UP group default 
        link/ether aa:c1:ab:1b:26:1a brd ff:ff:ff:ff:ff:ff link-netnsid 2
        inet 192.168.3.1/24 scope global eth1
           valid_lft forever preferred_lft forever
        inet6 fd01::2/64 scope global 
           valid_lft forever preferred_lft forever
        inet6 fe80::a8c1:abff:fe1b:261a/64 scope link proto kernel_ll 
           valid_lft forever preferred_lft forever                                                             
```
</p>
</details>

#### 5.3. Просмотр таблицы маршрутизации в VRF

<details><summary>ip route show vrf VRF_RED</summary>
<p>

```bash
    r1:/# ip route show vrf VRF_RED 
     192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.1 
     192.168.3.0/24 dev eth1 proto kernel scope link src 192.168.3.1                                                           
```
</p>
</details>

#### 5.4. ping внутри VRF

<details><summary>ip vrf exec VRF_RED ping 192.168.3.1</summary>
<p>

```bash
    r1:/# ip vrf exec VRF_RED ping 192.168.3.1
    PING 192.168.3.1 (192.168.3.1): 56 data bytes
    64 bytes from 192.168.3.1: seq=0 ttl=64 time=0.173 ms
    64 bytes from 192.168.3.1: seq=1 ttl=64 time=0.105 ms
    ^C
    --- 192.168.3.1 ping statistics ---
    2 packets transmitted, 2 packets received, 0% packet loss
    round-trip min/avg/max = 0.105/0.139/0.173 ms                                                        
```
</p>
</details>

#### 5.5. Удаление VRF

<details><summary>ip link del VRF_RED type vrf table 1001</summary>
<p>

</p>
</details>

#### 5.5. Просмотр существующих VRF

<details><summary>ip vrf</summary>
<p>
```bash
    r1:/# ip vrf
     Name              Table
     -----------------------
     VRF_RED           1001 
```
</p>
</details>

### 6. Создание/удаление subinterface 
```bash
 "Пример создание subinterface eth1.111 в vrf VFR_RED  назначить мак адрес"    
  ip link set dev eth1 up
  ip link add eth1.111 link eth1 type vlan id 111
  ip link set dev eth1.111 up
  ip link set dev eth1.111 master VRF_RED
  ip addr add 192.168.0.1/24 dev eth1.111
  ip link set dev eth1.111 address de:ad:be:ef:ca:fe
```

```bash
 "Удалить subinterface"
  ip link del eth1.111 link eth1
```

## 7. Команды для работы с бриджами
### 7.1. Через iproute2
Пример добавление в бридж eth0 и eth1

ip link add/ip link set 

<details><summary>добавление в бридж</summary>
<p>
```bash
   "Пример добавление в бридж eth0 и eth1"
    ip link add name br0 type bridge
    ip link set eth0 master br0
    ip link set eth1 master br0
    ip link set br0 up
```
</p>
</details>

Проверка bridge 

```
bridge link show
ip a show br0
ip a | grep br0
```

<details><summary>bridge link show</summary>
<p>
```bash
    bridge link show
     r1:/# bridge link show
      22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master br0 state forwarding priority 32 cost 2 
      37: eth1@if36: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 master br0 state forwarding priority 32 cost 2 
```
</p>
</details>

<details><summary>ip a show br0</summary>
<p>
```bash
    ip a show br0
     5: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
         link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::211:22ff:fe33:4455/64 scope link proto kernel_ll 
            valid_lft forever preferred_lft forever
```
</p>
</details>

<details><summary>ip a | grep br0</summary>
<p>
```bash
     ip a | grep br0
     5: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP group default 
     37: eth1@if36: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 qdisc noqueue master br0 state UP group default 
```
</p>
</details>


### 7.2 Через brctl
```bash
   brctl addbr br0
   brctl addif br0 eth0
   brctl addif br0 eth1
   ip link set br0 up
   brctl show
    r1:/# brctl show
     bridge name     bridge id               STP enabled     interfaces
     br0             8000.001122334455       no              eth0
                                                             eth1
  !Удаление бриджа
   brctl delbr br0
```

### 7.3. Просмотр fdb (forwarding data base)"
<details><summary>bridge fdb show br br0</summary>
<p>
```bash
   bridge fdb show br br0
    r1:/# bridge fdb show br br0
     33:33:00:00:00:01 dev br0 self permanent
     33:33:00:00:00:02 dev br0 self permanent
     01:00:5e:00:00:6a dev br0 self permanent
     33:33:00:00:00:6a dev br0 self permanent
     01:00:5e:00:00:01 dev br0 self permanent
     33:33:ff:33:44:55 dev br0 self permanent
     33:33:ff:00:00:00 dev br0 self permanent
     00:11:22:33:44:55 dev eth0 vlan 1 master br0 permanent
     00:11:22:33:44:55 dev eth0 master br0 permanent
     01:00:5e:00:00:01 dev eth0 self permanent
     33:33:00:00:00:02 dev eth0 self permanent
     33:33:00:00:00:01 dev eth0 self permanent
     33:33:ff:33:44:55 dev eth0 self permanent
     33:33:ff:00:00:00 dev eth0 self permanent
     aa:c1:ab:1b:26:1a dev eth1 vlan 1 master br0 permanent
     aa:c1:ab:1b:26:1a dev eth1 master br0 permanent
     01:00:5e:00:00:01 dev eth1 self permanent
     33:33:00:00:00:02 dev eth1 self permanent
     33:33:00:00:00:01 dev eth1 self permanent
     33:33:ff:1b:26:1a dev eth1 self permanent
     33:33:ff:00:00:02 dev eth1 self permanent
     33:33:ff:00:00:00 dev eth1 self permanent
```
</p>
</details>
 

## Команды для работы с вланами

## Команды для работы с VxLAN 


## PBR 
  Policy Base Route - механизм, позволдяющий маршрутизировать пакет взависимости от адреса источника

  Для работы PBR используется мехагнимзм таблиц, куда перенаправляются пакеты по правилам маршрутизации.

  Внутри каждой таблицы своя маршрутная табица маршрутов.

```bash
  Названия таблиц и их номер хранится в файле
   /etc/iproute2/rt_tables

  "ip rule show" Просмотреть текущие таблицы
  "ip rule list" Просмотреть текущие таблицы
   r1:/# ip rule show
    0:      from all lookup local
    1000:   from all lookup [l3mdev-table]
    32766:  from all lookup main
    32767:  from all lookup default

  "ip route show table <имя/номер>" Просмотреть конкретную таблицу.
   r1:/# ip route show table 0
    192.168.1.0/24 dev eth0 table 1001 proto kernel scope link src 192.168.1.1 
    local 192.168.1.1 dev eth0 table 1001 proto kernel scope host src 192.168.1.1 
    broadcast 192.168.1.255 dev eth0 table 1001 proto kernel scope link src 192.168.1.1 
    192.168.3.0/24 dev eth1 table 1001 proto kernel scope link src 192.168.3.1 
    local 192.168.3.1 dev eth1 table 1001 proto kernel scope host src 192.168.3.1 
    broadcast 192.168.3.255 dev eth1 table 1001 proto kernel scope link src 192.168.3.1 
    local 127.0.0.0/8 dev lo table local proto kernel scope host src 127.0.0.1 
    local 127.0.0.1 dev lo table local proto kernel scope host src 127.0.0.1 
    broadcast 127.255.255.255 dev lo table local proto kernel scope link src 127.0.0.1 
    local 192.168.2.1 dev eth2 table local proto kernel scope host src 192.168.2.1 
    anycast fd01:: dev eth1 table 1001 proto kernel metric 0 pref medium
    local fd01::2 dev eth1 table 1001 proto kernel metric 0 pref medium
    fd01::/64 dev eth1 table 1001 proto kernel metric 256 pref medium
    anycast fe80:: dev eth0 table 1001 proto kernel metric 0 pref medium
    anycast fe80:: dev eth1 table 1001 proto kernel metric 0 pref medium
    local fe80::211:22ff:fe33:4455 dev eth0 table 1001 proto kernel metric 0 pref medium
    local fe80::a8c1:abff:fe1b:261a dev eth1 table 1001 proto kernel metric 0 pref medium
    fe80::/64 dev eth0 table 1001 proto kernel metric 256 pref medium
    fe80::/64 dev eth1 table 1001 proto kernel metric 256 pref medium
    multicast ff00::/8 dev eth0 table 1001 proto kernel metric 256 pref medium
    multicast ff00::/8 dev eth1 table 1001 proto kernel metric 256 pref medium
    fd02::/64 nhid 17 via fdc0:192:168::2 dev eth2 proto bgp metric 20 pref medium
    fdc0:192:168::/64 dev eth2 proto kernel metric 256 pref medium
    fe80::/64 dev eth2 proto kernel metric 256 pref medium
    local ::1 dev lo table local proto kernel metric 0 pref medium
    anycast fdc0:192:168:: dev eth2 table local proto kernel metric 0 pref medium
    local fdc0:192:168::1 dev eth2 table local proto kernel metric 0 pref medium
    anycast fe80:: dev eth2 table local proto kernel metric 0 pref medium
    local fe80::a8c1:abff:fe60:5edb dev eth2 table local proto kernel metric 0 pref medium
    multicast ff00::/8 dev eth2 table local proto kernel metric 256 pref medium

  "ip rule add from <IP> table <N>" Добавить правило маршрутизации, для обработки определенных пакетов в других таблицах

  "Пример: для scr_ip 192.168.100.51 перенапрявлять пакеты в ens18 на gw 192.168.0.21" 
   echo 100 prov2 >> /etc/iproute2/rt_tables 
   ip route add default via 192.168.0.21 dev ens18 table prov2
   ip rule add from 192.168.100.51 lookup prov2

  "Правила можно посмотреть так:" 
   !root@clab:/home/icebale/clab/bgp-ula# ip rule list
    0:      from all lookup local
    32764:  from 192.168.100.51 lookup prov2
    32766:  from all lookup main
    32767:  from all lookup default

  "Таблица марщрутизации для таблицы 100 (proiv2) можно посмотреть так:"
   root@clab:/home/icebale/clab/bgp-ula# ip route show table 100
    default via 192.168.0.21 dev ens18 

  "Итого: при поступлении пакета с адресом источника 192.168.100.51" 
  "будет производиться lookup не по дефолтовой тааблице маршрутизации, а по таблице prov2 (100)"
```

## 8. tcpdump + wireshark
### 8.1. tcpdump - запись дампа в файл
```
tcpdump -s 0 -vi eth0 -w file.pcap
```

<details><summary>Разбор команды</summary>
<p>

```bash
  tcpdump -s 0 -vi eth0 -w file.pcap

  -s 0 (snaplen): Устанавливает длину захватываемого пакета.  
    Значение 0 означает захват пакета целиком (в современных версиях это эквивалентно 262144 байтам). 
    Это важно, чтобы данные внутри пакетов не обрезались.

  -v: Увеличивает детализацию вывода (verbose).
    ! при режиме записи в файл данный параметр полезен тем, что показывает сколько пакетов принято "Got 33" - что очень полезно для понимания 

  -i eth0 (interface): Указывает сетевой интерфейс.

  -w file.pcap (write): Записывает "сырые" пакеты в файл file.pcap 
    вместо вывода их текстового описания в терминал.
```

</p>
</details>

### 8.2. tcpdump - перенаправление в wireshark локально
```
sudo tcpdump -s 0 -Uli eth0  -w - | wireshark -k -i -
```

<details><summary>Разбор команды</summary>
<p>

```bash
  sudo tcpdump -s 0 -Uli eth0  -w - | wireshark -k -i -

  sudo: выполнение с правами суперпользователя (необходимо для доступа к сетевому интерфейсу).
  -s 0: устанавливает размер снимка (snaplen) в значение по умолчанию (обычно 262144 байт), 
    чтобы захватывать пакеты целиком, а не обрезать их.
  -U: включает "unbuffered" режим — пакеты отправляются в поток сразу по мере поступления, не дожидаясь заполнения буфера.
  -l: делает стандартный вывод (stdout) строчно-буферизованным для корректной передачи данных через конвейер (|).
  -i eth0: указывает интерфейс для прослушивания (eth0).
  -w -: записывает "сырые" данные в stdout вместо файла.
  |: передает вывод первой команды на вход второй.
  wireshark -k -i -: запускает Wireshark, где -k означает немедленное начало захвата, а -i - указывает использовать данные из стандартного ввода (stdin).

  для того, чтобы зайти в режим sudo надо изначально авторизоваться командами 
  "sudo su" - зайти как  su 
  "exit" - выход из su после
```

</p>
</details>

### 8.3. tcpdump - перенаправление в wireshark удаленно
Если tcpdump снимается с удаленного хоста, то можно организовать перенаправление сразу в локальный wireshark

Следующий набор команд позволяет перенаправить дамп с хоста удаленного интерейса на локальный wireshark
```
sshpass -p "MYPASS" ssh -q user@192.168.0.1 " echo 'MYPASS' | sudo -S tcpdump -s 0 -Uli ens18 -w - " | wireshark -k -i -
```

<details><summary>Разбор команды</summary>
<p>

```bash
 sshpass -p "MYPASS" ssh -q user@192.168.0.1 " echo 'MYPASS' | sudo -S tcpdump -s 0 -Uli ens18 -w - " | wireshark -k -i -

 Разбор компонентов:
  sshpass -p "MYPASS": Автоматизирует ввод пароля для SSH-соединения (sshpass - отдельная программа)
  ssh -q user@192.168.0.1: Подключается к удаленному серверу
    -q (quiet): Подавляет лишние сообщения (например, баннеры приветствия). 
                Это важно, чтобы в Wireshark летел только поток пакетов, а не текст "Welcome to Ubuntu".
  echo 'MYPASS' | sudo -S ...: Передает пароль для sudo, чтобы выполнить команду с правами суперпользователя без интерактивного запроса

  tcpdump -s 0 -Uli ens18 -w -:
  -s 0 :Захватывает пакет целиком (полный размер)
  -U :Пакетный вывод без буферизации (немедленная отправка) 
  -l :Линейная буферизация stdout
  -i ens18 :Интерфейс для прослушивания.
  -w - :Запись сырых данных в стандартный вывод (stdout) вместо файла.

  | wireshark -k -i - :Перенаправление данных в wireshark
  -k: Немедленный запуск захвата 
  -i -: Прием данных для анализа из стандартного ввода (stdin) [2, 4].

  !!!важно!!!
  пароль в данном случае передается в открытом виде и будет виден в history
  нужно учитывать эти риски при применении данного набора команд 
```

</p>
</details>


### 8.4. tcpdump - перенаправление в wireshark удаленно для интерфейса, находящегося в отдельном namespace
Следующий набор команд позволяет перенаправить дамп с даленного хоста для интерфейса, котоый находится в отдельном namespace (clab-bgp-ula-r1) перенаправить дамп на локальный wireshark

```
sshpass -p "MYPASS" ssh -q user@192.168.0.1 " echo 'MYPASS' | sudo -S ip netns exec clab-bgp-ula-r1 tcpdump -s 0 -Uli eth2 -w - " | wireshark -k -i -
```

<details><summary>Разбор компонентов</summary>
<p>

```bash
 Объяснение выполнения данного набора команд - сгенерировано ИИ. 
 Проверил - всё верно!) 

 sshpass -p "MYPASS" ssh -q user@192.168.0.1 " echo 'MYPASS' | sudo -S ip netns exec clab-bgp-ula-r1 tcpdump -s 0 -Uli eth2 -w - " | wireshark -k -i -

 Давайте разберем эту конструкцию «по кирпичикам». Она состоит из трех основных частей: аутентификация, захват трафика на удаленном узле и визуализация в Wireshark.
 1. sshpass -p 'MYPASS'
 Обычно SSH требует ручного ввода пароля. sshpass — это утилита, которая автоматически «подсовывает» пароль в ответ на запрос SSH.
 Зачем: Чтобы вся цепочка команд запускалась одной строкой без остановок.
 Нюанс: Это не самый безопасный способ (пароль виден в списке процессов ps). В идеале лучше использовать SSH-ключи.
 2. ssh -q user@192.168.0.1
 Стандартное подключение к удаленному серверу.
 -q (quiet): Подавляет лишние сообщения (например, баннеры приветствия). Это важно, чтобы в Wireshark летел только поток пакетов, а не текст "Welcome to Ubuntu".
 3. Удаленная команда (в кавычках)
 Здесь происходит "магия" внутри сервера:
 echo 'MYPASS' | sudo -S: Передает пароль команде sudo. Флаг -S заставляет sudo читать пароль из стандартного ввода (stdin), а не из терминала. Это нужно для запуска ip netns с правами root.
 ip netns exec clab-bgp-ula-r1: Выполняет последующую команду внутри конкретного сетевого пространства имен (Network Namespace). В средах вроде Containerlab (clab) каждый роутер — это отдельный namespace.
 tcpdump -i eth2 -s 0 -U -w -:
 -i eth2: Слушать интерфейс eth2.
 -s 0: Захватывать пакет целиком (snarf length 0 = unlimited).
 -U: (Packet-buffered) Отправлять каждый пакет в поток сразу, как только он получен, не дожидаясь заполнения буфера.
 -w -: Записывать «сырые» данные (pcap-формат) прямо в стандартный вывод (-), вместо сохранения в файл.
 4. | wireshark -k -i -
 Труба (|) перенаправляет двоичный поток данных от tcpdump (через SSH) на вход локальному Wireshark.
 -k (start immediately): Начать захват сразу после запуска.
 -i - (interface stdin): Сказать Wireshark, что данные нужно брать из стандартного ввода (stdin), а не с сетевой карты вашего компьютера.

 Итог: Вы видите пакеты с интерфейса eth2 роутера r1 в графическом интерфейсе своего Wireshark так, будто вы подключены к нему напрямую.
```

</p>
</details>


## 9.Команды диагностики сети

### 9.1. netstat

Показывает список соединений

netstat является уже устаревшей командой 
```
"netstat" - deprecated 
"ss" - current
```

Примеры использования
#### 9.1.1. netstat -anp | grep EST
Показывает все установленные соединения  
```
netstat -anp | grep EST
```

<details><summary>netstat -anp | grep EST</summary>
<p>
```bash
 netstat -anp | grep EST
  (Not all processes could be identified, non-owned process info
   will not be shown, you would have to be root to see it all.)
  tcp        0      0 127.0.0.1:10808         127.0.0.1:44072         ESTABLISHED 1526027/xray        
  tcp        0      0 127.0.0.1:10808         127.0.0.1:47818         ESTABLISHED 1526027/xray        
  tcp        0      0 127.0.0.1:33840         127.0.0.1:10808         ESTABLISHED -                   
  tcp        0      0 127.0.0.1:10808         127.0.0.1:33840         ESTABLISHED 1526027/xray        
  tcp        0      0 192.168.0.138:41380     208.123.190.202:443     ESTABLISHED 1526027/xray        
  tcp        0      0 192.168.0.138:46052     192.168.0.20:22         ESTABLISHED 4140849/ssh         
  tcp        0      0 127.0.0.1:44072         127.0.0.1:10808         ESTABLISHED -                   
  tcp        0      0 192.168.0.138:34582     192.168.0.20:22         ESTABLISHED 4112162/SecureCRT   
  tcp        0      0 10.33.0.2:47896         213.180.193.234:443     ESTABLISHED 438978/yandex_brows 
  tcp        0      0 127.0.0.1:10808         127.0.0.1:38646         ESTABLISHED 1526027/xray        
  tcp        0      0 10.33.0.2:42184         140.82.112.26:443       ESTABLISHED 16098/chrome --type 
  tcp        0      0 10.33.0.2:50488         213.180.204.82:443      ESTABLISHED 438978/yandex_brows 
  tcp        0      0 127.0.0.1:60922         127.0.0.1:10808         ESTABLISHED -                   
  tcp        0      0 10.33.0.2:48364         142.251.1.188:5228      ESTABLISHED 16098/chrome --type 
  tcp        0      0 10.33.0.2:38794         149.154.167.51:443      ESTABLISHED 410110/Telegram     
  tcp        0      0 127.0.0.1:10808         127.0.0.1:60922         ESTABLISHED 1526027/xray        
  tcp        0      0 127.0.0.1:47818         127.0.0.1:10808         ESTABLISHED -                   
  tcp        0      0 192.168.0.138:33720     208.123.190.202:443     ESTABLISHED 1526027/xray        
  tcp        0      0 192.168.0.138:48594     208.123.190.202:443     ESTABLISHED 1526027/xray        
  tcp        0      0 192.168.0.138:60294     208.123.190.202:443     ESTABLISHED 1526027/xray        
  tcp        0      0 127.0.0.1:38646         127.0.0.1:10808         ESTABLISHED -                   
  tcp        0      0 192.168.0.138:52228     208.123.190.202:443     ESTABLISHED 1526027/xray        
  tcp        0      0 192.168.0.138:60302     208.123.190.202:443     ESTABLISHED 1526027/xray        
  udp        0      0 192.168.0.138:68        192.168.0.1:67          ESTABLISHED -                   
```
</p>
</details>


#### 9.1.2. netstat -anp | grep LIST
Показывает все порты в состоянии LISTENED  
```
netstat -anp | grep LIST
```
<details><summary>netstat -anp | grep LIST</summary>
<p>
```bash
 netstat -anp | grep LIST
  (Not all processes could be identified, non-owned process info
   will not be shown, you would have to be root to see it all.)
  tcp        0      0 127.0.0.1:10808         0.0.0.0:*               LISTEN      1526027/xray        
  tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -                   
  tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
  unix  2      [ ACC ]     STREAM     LISTENING     635986   2860/gvfsd-trash     /run/user/1000/gvfsd/socket-XF4gywAV
  unix  2      [ ACC ]     STREAM     LISTENING     16637    -                    /run/containerd/s/6160a0cb6e37eea062ecca71cd03a536dcdad1ba040ead73a656701e1a294f5c
  unix  2      [ ACC ]     STREAM     LISTENING     7726     -                    /run/irqbalance/irqbalance757.sock
  unix  2      [ ACC ]     STREAM     LISTENING     20782    2591/Xorg            /tmp/.X11-unix/X1
  unix  2      [ ACC ]     STREAM     LISTENING     851879   2860/gvfsd-trash     /run/user/1000/gvfsd/socket-5WW7Mo4g
  unix  2      [ ACC ]     STREAM     LISTENING     17935    2737/gnome-session-  /tmp/.ICE-unix/2737
  unix  2      [ ACC ]     STREAM     LISTENING     3955     -                    /tmp/.EkolCb/s
  unix  2      [ ACC ]     STREAM     LISTENING     112372   16051/chrome         /tmp/.com.google.Chrome.pExDoT/SingletonSocket
  unix  2      [ ACC ]     STREAM     LISTENING     19203    2515/systemd         /run/user/1000/systemd/private
  unix  2      [ ACC ]     STREAM     LISTENING     19209    2515/systemd         /run/user/1000/bus
  unix  2      [ ACC ]     STREAM     LISTENING     19211    2515/systemd         /run/user/1000/gnupg/S.dirmngr
  unix  2      [ ACC ]     STREAM     LISTENING     19213    2515/systemd         /run/user/1000/gnupg/S.gpg-agent.browser
  unix  2      [ ACC ]     STREAM     LISTENING     19215    2515/systemd         /run/user/1000/gnupg/S.gpg-agent.extra
  unix  2      [ ACC ]     STREAM     LISTENING     19217    2515/systemd         /run/user/1000/gnupg/S.gpg-agent.ssh
  unix  2      [ ACC ]     STREAM     LISTENING     19219    2515/systemd         /run/user/1000/gnupg/S.gpg-agent
  unix  2      [ ACC ]     STREAM     LISTENING     19221    2515/systemd         /run/user/1000/pulse/native
  unix  2      [ ACC ]     STREAM     LISTENING     19223    2515/systemd         /run/user/1000/pipewire-0
  unix  2      [ ACC ]     STREAM     LISTENING     19225    2515/systemd         /run/user/1000/pk-debconf-socket
  unix  2      [ ACC ]     STREAM     LISTENING     19227    2515/systemd         /run/user/1000/snapd-session-agent.socket
  unix  2      [ ACC ]     STREAM     LISTENING     17800    2540/gnome-keyring-  /run/user/1000/keyring/control
  unix  2      [ ACC ]     STREAM     LISTENING     2524091  -                    /run/user/1000/.KKoIll/s
```
</p>
</details>


### 9.2. ss
Показывает список соединений

#### 9.2.1 ss -etnp
Показывет список процессов ESTABLISHED, tcp, порты в number, имена процессов 
```
"ss -etnp"
```

<details><summary>ss -etnp</summary>
<p>
```bash
"ss -etnp"
 State   Recv-Q   Send-Q       Local Address:Port         Peer Address:Port    Process                                                                                                                                           
 ESTAB   0        0                127.0.0.1:9090            127.0.0.1:36792    users:(("prometheus",pid=1410,fd=51)) timer:(keepalive,,0) uid:993 ino:13688 sk:1001 cgroup:/gitlab.slice/gitlab-runsvdir.service <->            
 ESTAB   0        0                127.0.0.1:9229            127.0.0.1:47306    users:(("gitlab-workhors",pid=1413,fd=7)) timer:(keepalive,192ms,0) uid:996 ino:14651 sk:1002 cgroup:/gitlab.slice/gitlab-runsvdir.service <->   
 ESTAB   0        0                127.0.0.1:9100            127.0.0.1:56398    users:(("node_exporter",pid=1409,fd=6)) timer:(keepalive,5.934ms,0) uid:993 ino:18826 sk:1003 cgroup:/gitlab.slice/gitlab-runsvdir.service <->   
 ESTAB   0        0                127.0.0.1:9168            127.0.0.1:39950    users:(("gitlab-exporter",pid=1411,fd=8)) uid:996 ino:13691 sk:1004 cgroup:/gitlab.slice/gitlab-runsvdir.service <->                             
 ESTAB   0        0                127.0.0.1:8082            127.0.0.1:48086    users:(("ruby",pid=1790,fd=13)) uid:996 ino:14654 sk:1005 cgroup:/gitlab.slice/gitlab-runsvdir.service <->                                       
 ESTAB   0        0                127.0.0.1:39950           127.0.0.1:9168     users:(("prometheus",pid=1410,fd=54)) timer:(keepalive,2.377ms,0) uid:993 ino:7529 sk:1006 cgroup:/gitlab.slice/gitlab-runsvdir.service <->      
 ESTAB   0        0             192.168.0.32:80          192.168.0.138:34812    users:(("nginx",pid=1464,fd=11)) uid:999 ino:439823 sk:4001 cgroup:/gitlab.slice/gitlab-runsvdir.service <->                                     
 ESTAB   0        0                127.0.0.1:51206           127.0.0.1:9121     users:(("prometheus",pid=1410,fd=48)) timer:(keepalive,10sec,0) uid:993 ino:5748 sk:1007 cgroup:/gitlab.slice/gitlab-runsvdir.service <->        
 ESTAB   0        0                127.0.0.1:36210           127.0.0.1:8060     users:(("prometheus",pid=1410,fd=11)) timer:(keepalive,,0) uid:993 ino:425716 sk:1008 cgroup:/gitlab.slice/gitlab-runsvdir.service <->           
 ESTAB   0        0                127.0.0.1:42654           127.0.0.1:9187     users:(("prometheus",pid=1410,fd=49)) timer:(keepalive,10sec,0) uid:993 ino:11651 sk:1009 cgroup:/gitlab.slice/gitlab-runsvdir.service <->       
 ESTAB   0        0                127.0.0.1:47306           127.0.0.1:9229     users:(("prometheus",pid=1410,fd=53)) timer:(keepalive,192ms,0) uid:993 ino:7528 sk:100a cgroup:/gitlab.slice/gitlab-runsvdir.service <->        
 ESTAB   0        0                127.0.0.1:56398           127.0.0.1:9100     users:(("prometheus",pid=1410,fd=56)) timer:(keepalive,5.934ms,0) uid:993 ino:7531 sk:100b cgroup:/gitlab.slice/gitlab-runsvdir.service <->      
 ESTAB   0        0                127.0.0.1:39964           127.0.0.1:9168     users:(("prometheus",pid=1410,fd=57)) timer:(keepalive,6.924ms,0) uid:993 ino:2490 sk:100c cgroup:/gitlab.slice/gitlab-runsvdir.service <->      
 ESTAB   0        0             192.168.0.32:80          192.168.0.138:34814    users:(("nginx",pid=1464,fd=20)) uid:999 ino:445222 sk:4002 cgroup:/gitlab.slice/gitlab-runsvdir.service <->                                     
 ESTAB   0        0                127.0.0.1:39966           127.0.0.1:9168     users:(("prometheus",pid=1410,fd=58)) timer:(keepalive,7.316ms,0) uid:993 ino:13999 sk:100d cgroup:/gitlab.slice/gitlab-runsvdir.service <->     
 ESTAB   0        0                127.0.0.1:48086           127.0.0.1:8082     users:(("prometheus",pid=1410,fd=55)) timer:(keepalive,4.141ms,0) uid:993 ino:7530 sk:100e cgroup:/gitlab.slice/gitlab-runsvdir.service <->      
 ESTAB   0        0             192.168.0.32:80          192.168.0.138:34826    users:(("nginx",pid=1464,fd=24)) uid:999 ino:433016 sk:4003 cgroup:/gitlab.slice/  
```
</p>
</details>

      

#### 9.2.2 ss -ltnp
Показывет список процессов LISTEN, tcp, порты в number, имена процессов 
```
ss -ltnp
```
<details><summary>ss -ltnp</summary>
<p>
```bash
 "ss -ltnp"
  State    Recv-Q   Send-Q     Local Address:Port     Peer Address:Port   Process                                                                                                                                      
  LISTEN   0        2048           127.0.0.1:8155          0.0.0.0:*       users:(("gitlab-kas",pid=1420,fd=8))                                                                                                        
  LISTEN   0        2048           127.0.0.1:8154          0.0.0.0:*       users:(("gitlab-kas",pid=1420,fd=13))                                                                                                       
  LISTEN   0        2048           127.0.0.1:8153          0.0.0.0:*       users:(("gitlab-kas",pid=1420,fd=11))                                                                                                       
  LISTEN   0        2048           127.0.0.1:8151          0.0.0.0:*       users:(("gitlab-kas",pid=1420,fd=10))                                                                                                       
  LISTEN   0        2048           127.0.0.1:8150          0.0.0.0:*       users:(("gitlab-kas",pid=1420,fd=12))                                                                                                       
  LISTEN   0        2048           127.0.0.1:8092          0.0.0.0:*       users:(("bundle",pid=1788,fd=24))                                                                                                           
  LISTEN   0        2048           127.0.0.1:8082          0.0.0.0:*       users:(("ruby",pid=1790,fd=10))                                                                                                             
  LISTEN   0        1024           127.0.0.1:8080          0.0.0.0:*       users:(("bundle",pid=1885,fd=19),("bundle",pid=1883,fd=19),("bundle",pid=1881,fd=19),("bundle",pid=1879,fd=19),("bundle",pid=1384,fd=19))   
  LISTEN   0        511              0.0.0.0:8060          0.0.0.0:*       users:(("nginx",pid=1464,fd=8),("nginx",pid=1463,fd=8),("nginx",pid=1462,fd=8),("nginx",pid=1461,fd=8),("nginx",pid=1401,fd=8))             
  LISTEN   0        2048       127.0.0.53%lo:53            0.0.0.0:*       users:(("systemd-resolve",pid=729,fd=15))                                                                                                   
  LISTEN   0        2048          127.0.0.54:53            0.0.0.0:*       users:(("systemd-resolve",pid=729,fd=17))                                                                                                   
  LISTEN   0        2048           127.0.0.1:9236          0.0.0.0:*       users:(("gitaly",pid=1482,fd=9),("gitaly",pid=1482,fd=8))                                                                                   
  LISTEN   0        2048           127.0.0.1:9229          0.0.0.0:*       users:(("gitlab-workhors",pid=1413,fd=3))                                                                                                   
  LISTEN   0        2048           127.0.0.1:9168          0.0.0.0:*       users:(("gitlab-exporter",pid=1411,fd=5))                                                                                                   
  LISTEN   0        2048           127.0.0.1:9187          0.0.0.0:*       users:(("postgres_export",pid=1421,fd=3))                                                                                                   
  LISTEN   0        2048           127.0.0.1:9100          0.0.0.0:*       users:(("node_exporter",pid=1409,fd=3))                                                                                                     
  LISTEN   0        2048           127.0.0.1:9090          0.0.0.0:*       users:(("prometheus",pid=1410,fd=6))                                                                                                        
  LISTEN   0        2048           127.0.0.1:9093          0.0.0.0:*       users:(("alertmanager",pid=1422,fd=7))                                                                                                      
  LISTEN   0        2048           127.0.0.1:9121          0.0.0.0:*       users:(("redis_exporter",pid=1423,fd=3))                                                                                                    
  LISTEN   0        511              0.0.0.0:80            0.0.0.0:*       users:(("nginx",pid=1464,fd=7),("nginx",pid=1463,fd=7),("nginx",pid=1462,fd=7),("nginx",pid=1461,fd=7),("nginx",pid=1401,fd=7))             
  LISTEN   0        2048             0.0.0.0:22            0.0.0.0:*       users:(("sshd",pid=1439,fd=3),("systemd",pid=1,fd=90))                                                                                      
  LISTEN   0        100            127.0.0.1:25            0.0.0.0:*       users:(("master",pid=1336,fd=13))                                                                                                           
  LISTEN   0        100                [::1]:25               [::]:*       users:(("master",pid=1336,fd=14))                                                                                                           
  LISTEN   0        2048                [::]:22               [::]:*       users:(("sshd",pid=1439,fd=4),("systemd",pid=1,fd=91))                                                                                      
  LISTEN   0        2048                   *:9094                *:*       users:(("alertmanager",pid=1422,fd=3)) 
```
</p>
</details>


#### 9.2.3. ss -K dst 192.168.0.138 dport = 40648
С помощью ss -K можно убить процесс, например к dst_ip 192.168.0.138 и dport 40648
```
ss -K dst 192.168.0.138 dport = 40648
```

<details><summary>ss -K dst 192.168.0.138 dport = 40648</summary>
<p>
```bash
 "ss -K dst 192.168.0.138 dport = 40648"
  Netid                  State                  Recv-Q                  Send-Q                                   Local Address:Port                                    Peer Address:Port                  Process                 
  tcp                    ESTAB                  0                       0                                         192.168.0.32:http                                   192.168.0.138:40648     
```
</p>
</details>

## Понятие namespace и его настройка