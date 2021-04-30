title: NX-OS PBR

# Настойка NS-OS PBR
Задача настройки PBR подобно настройке [Huawei PBR](https://icebale.readthedocs.io/en/latest/networks/huawei/platforms/NE8000/NE8000-config-cases/NE8000-PBR/)

## 1. Настройка на интерфейсе
**policy route-map PBR**
```
interface Vlan100
	description << Distribution DS100 >>
	ip address 1.1.1.1/31  
	policy route-map PBR  
```

## 2. Настройка route-map
Для ip адресов из ACL net_NAT0
производится маршрутизация трафика в соответствием с доступностью 
ip адреса текущего по логике работы NAT-a.
Если по трекеру NAT недоступен, то производится переход 
к проверки ip адреса следующего NAT-a

```bash
route-map PBR permit 100
  match ip address net_NAT1 
  set ip default next-hop verify-availability 192.168.1.1  track 1
  set ip default next-hop verify-availability 192.168.1.2  track 2
route-map Uzels5 permit 115
  match ip address net_NAT2 
  set ip default next-hop verify-availability 192.168.1.2  track 2
  set ip default next-hop verify-availability 192.168.1.1  track 1
```

## 3. ACL - тут балансится относительно клентской нагрузки в дистрибуциях 

```bash
ip access-list net_NAT0
  10 permit ip 172.0.0.0/24 any 
ip access-list net_NAT1
  210 permit ip 172.0.1.0/24 any 
```

## 4. track - трекер доступности ip адреса NAT

``` bash
track 1 ip route 192.168.1.1/32 reachability
 delay up 5 down 5
!
track 2 ip route 192.168.1.2/32 reachability
 delay up 5 down 5
```