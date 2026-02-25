title: Linux network commands


# Linux network commands
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
```bash
   "Пример добавление в бридж eth0 и eth1"
    ip link add name br0 type bridge
    ip link set eth0 master br0
    ip link set eth1 master br0
    ip link set br0 up
```

```bash
   "Проверка bridge" 
    bridge link show
     r1:/# bridge link show
      22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 master br0 state forwarding priority 32 cost 2 
      37: eth1@if36: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 master br0 state forwarding priority 32 cost 2 

    !r1:/# ip a show br0
     5: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
         link/ether 00:11:22:33:44:55 brd ff:ff:ff:ff:ff:ff
         inet6 fe80::211:22ff:fe33:4455/64 scope link proto kernel_ll 
            valid_lft forever preferred_lft forever
            
    !r1:/# ip a | grep br0
     5: br0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
     22: eth0@if23: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br0 state UP group default 
     37: eth1@if36: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 qdisc noqueue master br0 state UP group default 
```

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
   brctl addbr br0
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

## Понятие namespace и его настройка

## Команды диагностики сети
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
