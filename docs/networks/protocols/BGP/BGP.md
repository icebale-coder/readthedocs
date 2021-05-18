title: BGP

# Протокол BGP
Disclaimer:
```bash
Является на сегодняшний день единственным в природе 
EGP (External Gateway Protocol) протоколом маршутизации.
Занесен в "красную книгу" Интернета...

Берегите BGP!!! )))
```

## Определение/Назначение

Предназначен для передачи маршрутной информации как внутри AS **iBGP**, так и между AS **eBGP**.
"Де-факто", является единственным протокол динамической маршрутизации в сети Internet.

## Установление соединения
Соединение устанавливается по **tcp на порту 179**
Изначально любой сосед может инициироавть соединение если это в явном виде не запрещено в настройках.


## Таймеры протокола
Keepalive Interval — Интервал времени в секундах, между отправкой сообщений keepalive. По умолчанию 60 секунд.
Hold Time — Интервал времени в секундах, по истечении которого сосед будет считаться недоступным. По умолчанию 180 секунд.


## Формат протокола

Всё бессовестно содрано [отсюда](http://admindoc.ru/category/settings_cisco/bgp-cisco_books/) и [отсюда](http://xgu.ru/wiki/BGP) с небольшими собственными дополнениями - но лаконичнее этого сказать сложно!!! )))


## Типы сообщений в BGP

Тип сообщения передается в заголовке вида:
```bash
|<-------------------------- 32 бита --------------------------->|
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
+                                                               +
|                                                               |
+                                                               +
|                           Marker                              |
+                                                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Length               |      Type     |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

- Marker — поле, которое включено в заголовок для совместимости. Размер поля — 16 байт, все байты должны быть 1.
- Length — длина всего сообщения в октетах, включая заголовок. Поле может принимать значения от 19 до 4096.

- Type — тип передаваемого сообщения:

- 1 OPEN - используется для установки отношений соседства и обмена базовыми параметрами. 
    Отправляется сразу после установки TCP-соединения.
- 2 UPDATE - используется для обмена информацией о префиксах.
- 3 NOTIFICATION - используется когда возникают ошибки BGP. После отправки сообщения сессия с соседом разрывается.
- 4 KEEPALIVE - используется для проверки доступности соседа.
- 5 ROUTE-REFRESH - используется для передачи префиксов ORF. 


### OPEN - формат сообщения
OPEN (type = 1) - используется для установки отношений соседства и обмена базовыми параметрами. 

```bash
|<-------------------------- 32 бита --------------------------->|
+-+-+-+-+-+-+-+-+
|    Version    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     My Autonomous System      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Hold Time           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                         BGP Identifier                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Opt Parm Len  |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                                                               |
|                       Optional Parameters                     |
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

- Version — версия протокола BGP
- My Autonomous System — номер AS отправителя
- Hold Time — максимальное время в секундах, которое может пройти между получением Keepalive и сообщением Update
- BGP Identifier — RID (Router )
- Optional Parameters Length — длина опциональных параметров
- Optional Parameters - это Capability, т.е возможности передачи всяческих параметров.

Пример OPEN:

![bgp-update](../../img/bgp-open.jpg)

### UPDATE
UPDATE (type = 2) — используется для обмена информацией маршрутизации.

Формат сообщения Update:
```bash
+-----------------------------------------------------+
|   Unfeasible Routes Length (2 octets)               |
+-----------------------------------------------------+
|  Withdrawn Routes (variable)                        |
+-----------------------------------------------------+
|   Total Path Attribute Length (2 octets)            |
+-----------------------------------------------------+
|    Path Attributes (variable)                       |
+-----------------------------------------------------+
|   Network Layer Reachability Information (variable) |
+-----------------------------------------------------+
```

- Unfeasible Routes Length - длина невыполнимых маршрутов?
- Withdrawn Routes  - маршруты, которые нужно убрать из RIB BGP
- Total Path Attribute Length - общая длина атрибутов
- Path Attributes - атрибуты
- Network Layer Reachability Information (NLRI) - информация о самих префиксах

#### Примеры UPDATE:

##### Пример добавления префиксов 

![bgp-update](../../img/bgp-update-1.jpg)


##### Пример изъятия префиксов (withdraw)

![bgp-update](../../img/bgp-update-withdraw.jpg)

### NOTIFICATION
NOTIFICATION (type = 3) - используется когда возникают ошибки BGP. После отправки сообщения сессия с соседом разрывается.

```bash
|<-------------------------- 32 бита --------------------------->|

+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
| Error code    | Error subcode |           Data                |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+                               +
|                                                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

Error Code — тип оповещения:
```bash
1 — Message Header Error
2 — OPEN Message Error
3 — UPDATE Message Error
4 — Hold Timer Expired
5 — Finite State Machine Error
6 — Cease
```
### KEEPALIVE
KEEPALIVE (type = 4) - используется для проверки доступности соседа.

передается просто заголовок сообщения, где тип равен 4

![bgp-keepalive](../../img/bgp-keepalive.jpg)


### ROUTE-REFRESH 
ROUTE-REFRESH (type = 5) - используется для передачи префиксов ORF.

![bgp-route-refresh](../../img/bgp-route-refresh-1.jpg)


## Состояния связи с соседями

- **Idle** - изначальное состояние, в которое переходит BGP как только создается создается сосед в конфиге.
- **Connect** - BGP ожидает TCP соединение
- **Active** -  Инициализация TCP соединения, если соединение не установлено, 
   то состояние переходит в режим Connect. После нескольких попыток переходит инициализации переходит в состояние Idle.
- **Open sent** - BGP ожидает OPEN сообщения, после получения такого сообщения, 
    проверяются все обязательные атрибуты,если что-то не соответствует, то посылается сообщение NOTIFICATION сообщение с указанием ошибки.
    Если все ок, BGP начинает посылать сообщение KEEPALIVE. Если таймеры keepalive установлены у пиров разные,
    то устанавливается минимальное значение. сли на этой стадии обнаружен разрыв TCP сессии, 
    роутер переходит в состояние Active.
- **Open confirm** - после согласования таймеров keepalive на данной стадии дожидаемся прихода этого сообщения, 
                     если оно пришло, все ок, мы переходим к следующей стадии. 
- **Established** - означает, что соединение установлено, таймеры согласованы и  начинается обмен маршрутами.

**Дамп установления BGP и обмен префиксами + ORF можно посмотреть** [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/bgp+orf.pcapng)

## Результатом работы BGP

Результатом работы BGP является  - внесение в RIB лучших маршрутов, полученных по BGP.

Конечно с учетом наличия маршрутов от других протоколов, соответственно в RIB учитывается длина префикса и AD протокола, по которому этот префикс "приехал"...

Лучшие маршруты BGP вычисляются исходя из правил.
У каждого вендора этот механизм +- немного разнится

Приведу пример для Cisco
### Best Path Selection

![bgp-route-refresh](../../img/bgp-best-path-selection-cisco.jpg)


[Для RR имеются особенности при работе Best Path Selection](http://xgu.ru/wiki/BGP_route_reflector)
см. пункт 4. [атрибут Originator ID создает RR](https://sites.google.com/site/amitsciscozone/home/bgp/bgp-route-reflectors)

```bash
В тех случаях, когда маршрутизаторы могут получить один и тот же маршрут от RR 
и от обычного маршрутизатора, правила выбора лучшего маршрута BGP немного изменяются:

1. Первые шаги, в которых учитываются атрибуты weight, 
   local preference, origin и MED — остаются без изменений,
2. Если все предыдущие параметры одинаковы, 
   то маршруты полученные от eBGP-соседей предпочтительней, 
   чем маршруты полученные от iBGP-соседей.
3. Выбрать путь через ближайшего IGP-соседа
4. Выбрать путь через соседа с наименьшим BGP router ID. 
   Если у префикса есть атрибут Originator ID, 
   то вместо router ID сравнивается Originator ID.
5. Reflected-маршруты с более коротким cluster-list предпочтительнее 
   (длина cluster-list равна нулю, если маршрут без атрибута cluster-list),
6. Выбрать путь через соседа с наименьшим IP-адресом
```

общая схема работы BGP

![общая схема работы BGP](https://icebale.readthedocs.io/en/latest/networks/protocols/img/bgp-rib.jpg)


## MP-BGP
MP-BGP (MultiProtocol-BGP) - расширение протокола BGP, при котором есть возможность передавать другие AFI/SAFI.

По сути MP-BGP может работать со многими address family. Информация об address-family передается в полях AFI/SAFI.

- AFI  - Address Family Identifier - идентификатор семейства адресов
- SAFI - Subsequent Address Family Identifier - последующий идентификатор семейства адресов

Вот пример, какие семейства AFI/SAFI есть в BGP (их больше сейчас уже на самом деле)

![bgp-route-refresh](../../img/bgp-afi-safi.jpg)


## MP-REACH-NLRI vs NLRI

Следует обратить внимание, что для **AFI/SAFI = 1/1** - ipv4 unicast 
раздел NLRI в структуре сообщения находится на одном уровне с "Path Attributes",
а для других AFI/SAFI появляется специальный Path Attribute - MP_REACH_NLRI - в котором уже описан NLRI.
Все эти моменты описаны в [RFC-4760](https://datatracker.ietf.org/doc/html/rfc4760)

Разницу в структуре можно посмотреть тут:

![bgp-route-refresh](../../img/bgp-mpbgp-vs-bgp.jpg)




**Дамп c withdraw можно посмотреть** [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/bgp+withdraw.pcapng)

## Настройки

- [Базовая фильтрация + установка атрибутов](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/PMTUD)
- [ORF](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/ORF) 
- [Soft-reconfiguration](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/Soft-reconfiguration)
- [PMTUD](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/PMTUD)
- [Fall-over](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/Fall-over)
- [BGP PIC](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/Settings/BGP-PIC)




## Литература

- [1. BGP: Установление сессии между пирами](http://admindoc.ru/1153/session-peers/)
- [2. BGP](http://xgu.ru/wiki/BGP)
- [3. BGP route reflector](http://xgu.ru/wiki/BGP_route_reflector)