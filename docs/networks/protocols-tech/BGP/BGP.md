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

```bash
- Marker — поле, которое включено в заголовок для совместимости. Размер поля — 16 байт, все байты должны быть 1.
- Length — длина всего сообщения в октетах, включая заголовок. Поле может принимать значения от 19 до 4096.

- Type — тип передаваемого сообщения:

    - "1" - "OPEN" - используется для установки отношений соседства и обмена базовыми параметрами. 
             Отправляется сразу после установки TCP-соединения.
    - "2" -  "UPDATE" - используется для обмена информацией о префиксах.
    - "3" - "NOTIFICATION" - используется когда возникают ошибки BGP. 
             После отправки сообщения сессия с соседом разрывается.
    - "4" - "KEEPALIVE" - используется для проверки доступности соседа.
    - "5" - "ROUTE-REFRESH" - используется для передачи префиксов 
```
      [ORF](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/ORF/), а также для запросов о повторной передачи всех префиксов известных соседу при работе механизма [Route refresh](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/Route-Refresh/).

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
  Список "Capability Codes" со ссылками на их RFC можно посмотреть [тут](https://www.iana.org/assignments/capability-codes/capability-codes.xhtml) 

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


##### Пример изъятия префиксов (withdrawn)

![bgp-update](../../img/bgp-update-withdraw.jpg)


**Дамп c withdrawn можно посмотреть** [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/bgp+withdraw.pcapng)

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
В тех случаях, когда маршрутизаторы могут получить один и тот же маршрут 
от RR и от обычного маршрутизатора, правила выбора лучшего маршрута BGP 
немного изменяются:

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

![общая схема работы BGP](../../img/bgp-rib.jpg)


## MP-BGP
MP-BGP (MultiProtocol-BGP) - расширение протокола BGP, при котором есть возможность передавать другие AFI/SAFI.

По сути MP-BGP может работать со многими address family. Информация об address-family передается в полях AFI/SAFI.

- AFI  - Address Family Identifier - идентификатор семейства адресов
- SAFI - Subsequent Address Family Identifier - последующий идентификатор семейства адресов

Вот пример, какие семейства AFI/SAFI есть в BGP (их больше сейчас уже на самом деле)

![bgp-afi-safi](../../img/afi-safi.jpg)


## MP-REACH-NLRI vs NLRI

!!!warning "Важно"
          Следует обратить внимание, что для **AFI/SAFI = 1/1** - ipv4 unicast 
          раздел NLRI в структуре сообщения находится на одном уровне с "Path Attributes",
          а для других AFI/SAFI появляется специальный Path Attribute - MP_REACH_NLRI,
          в котором уже описан NLRI. 
          Все эти моменты описаны в [RFC-4760](https://datatracker.ietf.org/doc/html/rfc4760)

Разницу в структуре можно посмотреть тут:

![bgp-route-refresh](../../img/bgp-mpbgp-vs-bgp.jpg)

## BGP Типы атрибутов

- [1. BGP Attributes List](https://www.networkers-online.com/blog/2012/05/bgp-attributes/)

- [2. Атрибуты BGP](http://xgu.ru/wiki/%D0%90%D1%82%D1%80%D0%B8%D0%B1%D1%83%D1%82%D1%8B_BGP)

- [3. BGP origin](http://xgu.ru/wiki/BGP_origin)

```bash
"Well known BGP attribute types (Типы well-known атрибутов)":
  - "Well-known mandatory": обязательный атрибут - все маршрутизаторы, 
    работающие по протоколу BGP, должны распознавать эти атрибуты. 
    Данные атрибуты должны присутствовать во всех BGP Update:
    -"1 ORIGIN" - указывает на то, каким образом был получен маршрут в обновлении.
```

![bgp-attribute-origin](../../img/bgp-attribute-origin.jpg)


```bash    
    -"2 AS_PATH"  - список AS, чз которые прошел префикс
    -"3 NEXT_HOP" - ip адрес next-hop-a для достижения префикса

  - "Well-known discretionary": необязательный атрибут - все маршрутизаторы, 
    работающие по протоколу BGP, должны распознавать эти атрибуты. 
    Могут присутствовать в BGP update, но их присутствие не обязательно:
    -"5 LOCAL_PREF" - атрибут определяет 
      предпочтение для данного префикса в виде целого числа, value <0-4294967295>  
      по умолчанию LP = 100  
    -"6 ATOMIC_AGGREGATE" - Назначение атрибута - предупредить узлы BGP на пути о том, 
    что некоторая информация была потеряна из-за процесса агрегации маршрутов 
    и что совокупный путь может быть не лучшим путем к месту назначения. 

"Optional BGP attribute types (Типы опциональных атрибутов)":
  - "Optional transitive (транзитивный атрибут)": необязательный атрибут, 
    может не распознаваться всеми реализациями BGP. 
    Если маршрутизатор не распознал атрибут, 
    он помечает обновление как частичное (partial) 
    и отправляет его дальше соседям, сохраняя не распознанный атрибут:
    -"7 AGGREGATOR"  - устанавливается равным RID маршрутизатора, 
      который выполнил агрегирование (ATOMIC_AGGREGATE).
    -"8 COMMUNITY" - атрибут, который несет в себе информацию для политик фильтрации соседей
     это числовое значение, которое может быть присвоено определенному префиксу и объявлено другим соседям. 
     Когда сосед получает префикс, он проверяет значение community и предпринимает определенные им действия
     (например, фильтрует или изменяет атрибуты, для префикса с данным community).
     BGP community задаются в формате ASN:VALUE - (AS NUMBER: VALUE), от [1:0 до 65534:65535]
     "BGP community бывают":
       - BGP well-known community
          - "Internet": анонсировать всем маршрутизаторам.
          - "Local-as": разрешено рассылать анонсы только внутри AS, за пределы своей AS рассылка запрещена.
          - "No-Advertise": никому не анонсировать префиксы с таким cоmmunity
          - "No-Export": не анонсировать префиксы с таким community внешним AS.
       
       - прочие BGP - тут уже политика поведения задаётся самим сетевым инженером, в зависимости от задач.


   - "Optional non-transitive (нетранзитивный атрибут)": необязательный атрибут, 
     могут не распознаваться всеми реализациями BGP. 
     Если маршрутизатор не распознал атрибут, то атрибут игнорируется 
     и не передается соседям:
     -"4 MULTI_EXIT_DISC (MED)" Optional non-transitive
     -"9 ORIGINATOR_ID" -  
     -"10  Cluster List"  Optional non-transitive 
     -"14  Multiprotocol Reachable NLRI"  Optional non-transitive
     -"15  Multiprotocol Unreachable NLRI"  Optional non-transitive

```

![bgp-attribute-types](../../img/bgp-attribute-types.jpg)

![bgp-attribute-type-schema](../../img/bgp-attribute-type-schema.jpg)


## eBGP vs iBGP

https://linkmeup.ru/blog/1201/

https://forum.huawei.com/enterprise/en/understanding-of-loop-protection-for-the-bgp-protocol/thread/623792-861

```bash
"eBGP (external BGP)" - BGP соединение между разными AS.
"iBGP (internal BGP)" - BGP соединение между одинаковыми AS.


"eBGP vs iBGP характерны следующие особенности поведения:"
1. При передаче префиксов в eBGP анонсе меняется "next hop" (mandatory атрибут) 
   на ip адрес стыковочного интерфейса. Для iBGP "next hop" не меняется, 
   хотя можно указать это вручную с помощью команды "туче рщз ыуда" (next hop self) 
2. Разное поведение при передачи атрибутов:
   - атрибут LP при наличии его в eBGP Update будет игнорироваться второй стороной
3. При передаче по eBGP добавляется AS в AS-PATH. 
   При передаче между участниками iBGP AS в AS-PATH не добавляется
4. Разная/Одинаковая AD: 
   - Разная (cisco, huawei):  eBGP = 20, iBGP =   200
   - Одинаковая (HP):  eBGP = 255, iBGP =  255
   - Одинаковая (Arista): eBGP = 200, iBGP =  200
5. По разному работает правило split-horizon, помимо неотдачи анонсов назад: 
   - анонсы, полученные от eBGP сессии, передаются всем другим BGP соседям как eBGP, так и iBGP
   - анонсы, полученные от iBGP сессии, не передаются другим участникам iBGP 
     (за исключением правил поведения для RR).
6. Для связности внутри iBGP используется full-mesh топология (полносвязная топология) - 
   каждый маршрутизатор устанавливает iBGP сессию с каждым.
   Альтернативный вариант организации связности между участниками iBGP - это связь чз RR (Route Reflector)
   Также возможно создание BGP конфедераций:
    - внутри конфедерации своя собственная Sub-AS (из приватного диапазона AS)
      внутри конфедерации получается iBGP.
      Конфедерации общаются между собой по eBGP, так номера sub-AS разные. 
      В точке выхода во внешнюю AS из AS-PATH снимаются sub-AS-ки и отпавляет только реальные.
```
Проиллюстрировать конфедерации можно как-то так...

![bgp-confederation](../../img/bgp-confederation.jpg)

```bash
6. По разному работает loop prevention:
   - для eBGP - при наличие в атрибуте AS-PATH собственной AS данный анонс дропнется соседом.
   - для iBGP - правило split-horizon, которое не дает рассылать анонсы, 
     полученные по iBGP соседям.
   - Для RR: 
     - при передачи BGP анонсов чз RR добавляется дополнительный атрибут Cluster_ID, 
       который добавляется в Culster list. Если RR принимает от соседа анонс, 
       в котором присутствует его собственный Cluster_ID, то такой анонс дропается.
```

![ibgp-rr-loop-prevent-cluster-id](../../img/ibgp-rr-loop-prevent-cluster-id.jpg)


```bash
     - при передачи BGP анонсов чз RR добавляется дополнительный атрибут Originator_ID,
       по сути это Router_ID того маршрутизатора, кто породил данный анонс, 
       если вдруг такой анонс долетит до того маршрутизатора, который его породил,
       то маршрутизатор такой анонс отбросит.
```

![ibgp-rr-loop-prevent-originator-id](../../img/ibgp-rr-loop-prevent-originator-id.jpg)

```bash
Общая таблица сравнения различных режимов работы RR:
```

![ibgp-rr](../../img/bgp-rr.jpg)

![ibgp-rr](../../img/bgp-update-from-RR.jpg)
Как видно префиксы "рожденные" на непосредственном соседом приходят без доп атрибутов "Originator-ID" и "Cluster-List"
а маршруты отраженные от соседа уже имеют доп атрибутов "Originator-ID" и "Cluster-List". 
Дампы прилагаются:
- [bgp without RR client](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/bgp-with-out-RRclient.pcapng)
- [bgp with RR client](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/bgp-with-RRclient.pcapng)


## Internet eXchange (IX) площадки
```bash

В Internet eXchange (IX) площадках используется терминология "Route Client" и "Route Server".

Особенностями работы Internet eXchange площадок, является их отличие 
от стандартного поведения iBGP, eBGP и RR.
 
  "Route Server" - работает таким образом, что при передаче BGP-анонса:
      - атрибут "next-hop" не меняется и содержит ip адрес того маршрутизатора, 
        от которого данный анонс были получен RS, хотя соединение между "Route Client" 
        и "Route Server" является eBGP. 
      - атрибут "AS-PATH" передается без изменений. 
        Таким образом, обмен трафиком участников происходит напрямую, минуя RS. 

В таком случае для связности всех клиентов помещают в одну сеть, например в сеть /24.
```

## Настройки

- [Базовая фильтрация + установка атрибутов](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/PMTUD)
- [ORF](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/ORF) 
- [Soft-reconfiguration](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/Soft-reconfiguration)
- [PMTUD](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/PMTUD)
- [Fall-over](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/Fall-over)
- [BGP PIC](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/BGP/Settings/BGP-PIC)




## Литература

- [1. BGP: Установление сессии между пирами](http://admindoc.ru/1153/session-peers/)
- [2. BGP](http://xgu.ru/wiki/BGP)
- [3. BGP route reflector](http://xgu.ru/wiki/BGP_route_reflector)
