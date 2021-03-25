title: BGP

# Протокол BGP

## Определение/Назначение

## Настройки
### ORF  
**orf - output route filters**

```bash

```
  Механизм, который позволяет посылать соседу информацию о тех префиксах, которые хочет принимать вторая сторона.
  Удобно в случае, когда идет фильтрация входящих префиксов. При включенном orf с обоих сторон происходит обмен фильтрами на входящие префиксы и сосед заранее при отправке префиксов сделает на своей стороне фильтрацию лишних (не нужных соседу) и отправит только необходимые.

#### Реализация на оборудовании
##### Juniper
**MX80**

```bash
neighbor 2.2.2.2 {
    description "== NEIGHBOR with ORF ==";
    local-address 1.1.1.1;
    peer-as 22222;
    outbound-route-filter {
        bgp-orf-cisco-mode;
        prefix-based {
            accept {
                inet;
            }
        }
    }
}
```
!!! note "Примечание"
	С данной настройкой не удалось подружить Джун с Хуавеем. Нужно сделать лабу


##### Cisco
**IOS/IOS-XE**

```bash
     router bgp 11111
      address-family ipv4
        neighbor 1.1.1.1 capability orf prefix-list both
```
подробнее см. [документацию](http://admindoc.ru/1250/bgp-outbound-route-filtering/)

**IOS-XR**
В IOS-XR надо в явном виде указывать prefix-set для ORF

```bash
  prefix-set orf-preset
    172.16.1.0/24,
    172.16.5.0/24,
    172.16.11.0/24
  end-set
  
  route-policy policy-orf
    if orf prefix in orf-preset then
      drop
    endif
    if orf prefix in (172.16.3.0/24, 172.16.7.0/24, 172.16.13.0/24) then
      pass
    endif
  
  router bgp 2
    neighbor 1.1.1.1
      remote-as 3
      address-family ipv4 unicast
        orf route-policy policy-orf
```

подробнее см. [документацию](https://www.cisco.com/c/en/us/td/docs/routers/xr12000/software/xr12k_r4-0/routing/configuration/guide/rc40xr12k_chapter7.html)

##### Huawei
**NE8000-F1A**

```bash
    bgp 11111
     ipv4-family unicast
      peer 1.1.1.1 capability-advertise orf ip-prefix both
```
подробнее см. [документацию](https://support.huawei.com/enterprise/en/doc/EDOC1100146591/ec511dde/configuring-prefix-based-bgp-orf)