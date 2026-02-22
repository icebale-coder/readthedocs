title: Linux network commands

Disclaimer:
```bash
Всё что есть в сетевых вендорах не более чем форк *nix...
```

!!! note "JFYI"
		В данном разделе будет отчасти дублироваться команды из раздела "Linux commands", 
        чтобы сгруппировать команды по работе с сетью в одном месте.


## Команды работы с интефейсами

### 1. Просмотр состояния интерфейсов 
!!! NB
  команды
  ifconfig          - depricated
  ip (из iproute2)  - current 


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





## Команды для работы с бриджами

## Команды для работы с вланами

## Команды для работы с VxLAN 

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
