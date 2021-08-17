title: Базовые настройки

# Базовые настройки

## Фильтрация + установка атрибутов

!!! warning "Важно"
    Для Cisco prefix-list, route-map - работают по дефолту по принципу **implicit-deny** и этот момент забывать нельзя!
    Хотя есть [ньюансы](https://icebale.readthedocs.io/en/latest/networks/cisco/Cisco.cases/route-map/#_5)

!!! warning "Важно"
    Для Juniper policy-statement (аналог route-map)  - работают по дефолту по принципу **implicit-accept** и этот момент забывать нельзя тоже! )

Приведу базовые настройки на примере оборудовании Cisco

### prefix-list
Позволяет произвести фильтрацию префиксов 

```bash
ip prefix-list PL-CUSTOMER-IN seq 10 permit 192.168.0.0/23 ge 24
ip prefix-list PL-CUSTOMER-IN seq 20 permit 192.168.10.0/24
!
ip prefix-list PL-DEFAULT-ONLY seq 10 permit 0.0.0.0/0
!
router bgp 31257
 neighbor 192.168.1.1 remote-as 65000
 neighbor 192.168.1.1 description == Customer ==
 !
 address-family ipv4
  neighbor 192.168.1.1 prefix-list PL-CUSTOMER-IN in
  neighbor 192.168.1.1 prefix-list PL-DEFAULT-ONLY out
```

**Логика работы:**

- Принимаем от клиента возможные префиксы 
```bash
  - 192.168.0.0/23
  - 192.168.0.0/24
  - 192.168.1.0/24
  - 192.168.10.0/24
```
- Отдаем только дефолт

### route-map + set
Позволяет произвести фильтрацию префиксов и назначить/изменить им атрибуты

Немного "разукрасим", зададим условия, что мы хотим еще менять/добавлять атрибуты BGP

```bash
ip prefix-list PL-CUSTOMER-PREFIX-1 seq 10 permit 192.168.0.0/23 ge 24
!
ip prefix-list PL-CUSTOMER-PREFIX-2 seq 10 permit 192.168.10.0/24
!
ip prefix-list PL-DEFAULT-ONLY seq 10 permit 0.0.0.0/0
!
ip prefix-list PL-UPLINK-OUT seq 10 permit 0.0.0.0/0 le 24
!
route-map RM-CUSTOMER-IN permit 10
 match ip address prefix-list PL-CUSTOMER-PREFIX-1
 set local-preference 150
route-map RM-CUSTOMER-IN permit 20
 match ip address prefix-list PL-CUSTOMER-PREFIX-2
 set local-preference 300
!
route-map RM-UPLINK-OUT permit 10
 match ip address prefix-list PL-UPLINK-OUT
 set as-path prepend 31257 31257 31257
!
router bgp 31257
 neighbor 1.1.1.1 remote-as 11111
 neighbor 1.1.1.1 description == Uplink ==
 neighbor 192.168.1.1 remote-as 65000
 neighbor 192.168.1.1 description == Customer ==
 !
 address-family ipv4
  neighbor 1.1.1.1 route-map UPLINK-OUT out
  neighbor 192.168.1.1 route-map PM-CUSTOMER-IN in
  neighbor 192.168.1.1 prefix-list PL-DEFAULT-ONLY out
```

**Логика работы:**

- Принимаем от клиента возможные префиксы и назначаем для них LP=150
```bash
  - 192.168.0.0/23
  - 192.168.0.0/24
  - 192.168.1.0/24
```

- Принимаем от клиента возможные префиксы и назначаем для них LP=300
```bash
  - 192.168.10.0/24
```

- Отдаем клиенту только дефолт
- Отдаем аплинку префиксы с доп. тремя препендами в AS-PATH 
