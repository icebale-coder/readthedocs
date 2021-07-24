title: STP и все,все, все...

# STP - Spining Tree Protocol
## Определение/Назначение
Протокол предназначен для защиты от петель L2 при подключение резервных/избыточных линков между коммутаторами. 

Descrambler:
```bash
Если в сети используется протокол STP или его собратья (RSTP,MSTP,PVST, PVST+), 
то это значит, что дизайн сети неправильный!
!!!И НУЖНО СРОЧНО МЕНЯТЬ ДИЗАЙН СЕТИ!!!
```

## Общий принцип работы:
В основу работы протокола поставлен принцип построения древовидного графа с единым корнем.
Таким образом, строится топология, которая по определению не имеет петель.
Избыточные порты, которые не участвуют в топологии этого дерева, являются резервными и в случае 
выпадания какого либо порта из топологии дерево перестраивается и задейтвует резервные порты

## Разновидности протокола

## Таймеры протокола:
```java
update timer - after that broadcast sent 30sec (период отправки сообщений типа "Response")
invalid timer - after expire route declare as a invalid 180sec 
               (Когда маршрут находится в таблице маршрутизации в состоянии possibly down, 
               	это значит, что Invalid timer истек, а Flush timer еще нет)
hold down - after hold down timer expire 180sec
Flush timer - after expire route entry deleted form routing table 240sec
```

```java
Кароче, теперь па-Русски! )
Что происходит по таймингу:
1. Если в течении 30 секунд не приходит очередной Update от соседа, 
   то маршруты от него метятся в RIB как "possibly down".
2. Если в течении 240 секунд не приходит очередной Update от соседа, 
   то маршруты от него удаляются из RIB.
```


### Пример "possibly down" в RIB
![Request](img/rip/rip-possibly-down.jpg)

### Общая схема работы таймера:
![ip-header](img/rip/timers.jpg)

Структура протокола очень простая:
```bash
- Command
- Version
- Информация об ip адресах.
```

!!!note "Важный факт" 
		Из особенностей работы: по факту при "протухании" маршрутов по RIP на маршрутизаторе не напрямую соединенном с другим.
		Непосредственно соединенный маршрутизатор посылает сообщении "Response" (оно же "Update") с всеми актуальными маршрутами, но уже без тех, которые "протухли"... А не например как в BGP с помощью "withdraw", в котором указываются только отменненые маршруты...

## Конфигурация RIP на оборудовании cisco

```bash
router rip
 version 2
 network 55.0.0.0
 network 192.168.5.0
 network 192.168.55.0
```
В секции router rip перечисляются сети которые будут участвовать в rip.
По желанию, там же тюнятся таймеры.

Особенности синтаксиса: в конфигурации rip v2 указываются только сети без масок, но по факту при обращении к интерфейсу берутся правильные маски в rip database.

## Литература

- [1. RIP](http://xgu.ru/wiki/RIP)
- [2. RIP V1 vs V2](https://ipwithease.com/rip-v1-vs-rip-v2/)
- [3. RIP General Operation, Messaging and Timers](http://www.tcpipguide.com/free/t_RIPGeneralOperationMessagingandTimers-2.htm)
- [4. cisco forum](https://learningnetwork.cisco.com/s/question/0D53i00000Kt6cX/rip-timers)

Пример дампа rip в wireshark можно посмотреть [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/rip2.pcapng)
