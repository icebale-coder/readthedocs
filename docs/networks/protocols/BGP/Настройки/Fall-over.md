title: Fall-over

# Fall-over

## Назначения механизма

Fall-over - механизм, который позволяет оптимизировать время сходимости (convergence) BGP протокола за счет наблюдения за наличием соседа в таблице маршрутизации. 

Пример работы: если BGP сосед вдруг перестал быть доступен в таблице маршрутизации, то BGP сессия сразу отключается, таким образом по итогу сразу же происходит актуализация информации о маршрутах BGP в RIB не дожидаясь отработки таймеров BGP (keepalive interval и holdtime), которые достаточно большие по дефолту (60, 180)

Механизм "fall-over" используется для iBGP, когда адреса BGP соседей - это loopback соседа и соответсвенно маршрут о данном лупбеке передается в протоколе IGP.

Разберем работу механизма "fall-over" на конкретных примерах:
Примеры настройки взяты из [статьи](https://networkop.co.uk/blog/2015/06/11/ibgp-fallover-trick/)

### Настройка - пример 1:
Пример настройки
```bash
router bgp 100
 neighbor 1.1.1.1 remote-as 100
 neighbor 1.1.1.1 fall-over 
 neighbor 2.2.2.2 remote-as 200
 neighbor 2.2.2.2 fall-over 
!
```
!!! warning "Важно"
    При такой настройке, "fall-over" работает только в том случае, 
    если нет "less specific", маршрута в RIB,  например в виде "корзины", 
    т.к. иначе "next hop" в явном виде в таблице маршрутизации.

Чтобы исключить зависимость от наличия default-а в таблице маршрутизации рассмотрим следующий пример:

### Настройка - пример 2:
Пример настройки с уникальным route-map для каждого BGP соседа

```bash
router bgp 100
 neighbor 1.1.1.1 remote-as 100
 neighbor 1.1.1.1 fall-over route-map RM-BGP-FALLOVER-1
 neighbor 2.2.2.2 remote-as 200
 neighbor 2.2.2.2 fall-over route-map RM-BGP-FALLOVER-2
!
ip prefix-list PL-ROUTER-1 seq 5 permit 1.1.1.1/32
!
ip prefix-list PL-ROUTER-2 seq 5 permit 2.2.2.2/32
!
route-map RM-BGP-FALLOVER-1 permit 10
 match ip address prefix-list PL-ROUTER-1
!
route-map RM-BGP-FALLOVER-2 permit 10
 match ip address prefix-list PL-ROUTER-2
```
В данном примере используется специальный route-map, который матчит ip адрес BGP соседа не со всей RIB, а только с нужным маршрутом.
Такой конфиг уже устойчив к наличию дефолта и уже таргетно фильтрует из RIB ip адрес каждого соседа.
Но также имеется и недостаток в таком конфиге, т.к. для каждого соседа нужно делать свой route-map.

Чтобы решить этот вопрос используют более унифицированный конфиг:
### Настройка - пример 3:
Пример настройки с универсальным route-map для каждого BGP соседа

```bash
!
router bgp 100
 neighbor 1.1.1.1 remote-as 100
 neighbor 1.1.1.1 fall-over route-map RM-BGP-FALLOVER
 neighbor 2.2.2.2 remote-as 200
 neighbor 2.2.2.2 fall-over route-map RM-BGP-FALLOVER
!
ip prefix-list PL-ALL-LOOPBACKS seq 5 permit 0.0.0.0/0 ge 32
!
route-map RM-BGP-FALLOVER permit 10
 match ip address prefix-list PL-ALL-LOOPBACKS
!
```

В данном примере в route-map RM-BGP-FALLOVER попадают только префиксы с маской /32 - по сути это и есть ip адреса всех известных в RIB лупбеков.
Логика работы проста - если ip адреса соседа нет в RIB, то и в route-map его тоже не будет и в таком случае запустится механизм fall-over.
