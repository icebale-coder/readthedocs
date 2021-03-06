title: Дизайн сети

# Дизайн сети L3
Здесь попробую собрать дизайн сетей, используемых в моей практике

Схема организации работы сети на L3 уровне

![topo-L3](../img/topo-l3.jpg)

## Назначение/роли устройств
### 1. BGW - Boarder GateWay - пограничный маршрутизатор
### 2. CR - Core Router - маршрутизатор уровня ядра сети
### 3. DS - Distribute Switch - L3 switch уровня дистрибуции

## Организация работы сети
В сети используется два протокола маршрутизации: IGP и iBGP

### 1. IGP протокол
Используется для связности внутри сети между служебными ip адресами по стыковочным интерфейсам настраивается один из IGP протоколов (EIGRP, OSPF, IS-IS)
Задача IGP протокола распространять информацию о стыковочных сетях и лупбек интерфейсах маршрутизаторов сети (BGW, CR, DS)

### 2. iBGP протокол
Используется для связности внутри сети между клиентскими ip адресами. Сессии устанавливаются между лупбек адресами маршрутизаторов.
В префиксах BGP распространяется информация о клиентских сетях.

### 3. Принцип огранизации маршрутизации
Каждая дистрибуция устанавливает BGP соседство с CR1/2. 
CR1/2 являются RR для каждой из дистрибуций DS, благодаря чему каждая дистрибуция знает о всех клиентских маршрутах в сети.

От CR1/2 передает по iBGP на BGW1/2 информация о клиентских сетях. От BGW1/2 на CR1/2 по iBGP передается только дефолтовый маршрут.
Этот дефолтовый маршрут передается дальше по iBGP на уровень дистрибуций.

Все iBGP устанавливают соседство по лупбеку lo0, такой способ удобен для организации резервирования маршрутов по IGP протоколу.
Например, при обрыве линка CR1-DS0, связность по iBGP останется благодаря работе IGP через путь CR1-CR2-DSO.

Для передачи информации об абонентских сетях между CR1/2 настраиваются как RR клиенты для друг друга.
Также для передачи информации о клиентских сетях BGW1/2 настраиваются друг для друга как RR клиенты.

### Тюнинг протоколов
Для работы по такому принципу необходимо тюнить сходимость по протоколам IGP и iBGP.

Наиболее распространенный вариант тюнинга:
#### IGP

- На стыковочных интерфейсах настраивается протокол [BFD](https://icebale.readthedocs.io/en/latest/networks/protocols/BFD), к которому привязывается протокол IGP
- Тюнятся таймеры самого IGP протокола

#### iBGP

- для iBGP протокола настраивается функционал [fall-over](https://icebale.readthedocs.io/en/latest/networks/protocols/BGP/#fall-over)
Что обеспечивает ускорение реакции протокола BGP при полном отвале соседа.

- Также до приемлемого состояния реакции уменьшаются стандартные таймеры протокола BGP (keepalive и holdtime)

#### Пример работы связки протоколов
Таким образом алгоритм работы при таком тюнгинге будет выглядеть следующим образом (разберу на примере DS0):

У DS0 по IGP известны маршруты до lo0 CR1/2.

При отсутсвии связности между CR1-DS0 сначала отрабатывает BFD протокол, который уведомляет CR1 и DS0 о недоступности стыковочных ip адресов. Извещенный об этом IGP протокол не дожидаясь своих таймеров рвет соседство и записи в RIB о стыковочных адресах CR1-DS0 удаляются.
При этом BGP связность не успеет разорваться, т.к. лупы (lo0) обоих маршрутизаторов будут доступны через "обходной" путь изученный по IGP.

В случае же когда полностью отвалится CR1 или CR2, то сначала отработает BFD, затем разберутся IGP, соответственно из RIB полностью уйдет информация о доступности соседского lo0 и в таком случае уже включится в работу механизм BGP "fall-over", который не дожидаясь отработки своих таймеров (keepalive и holdtime) порвет сессию с недоступным по BGP соседом. В таком случае останется только одна сессия с "живым" CR2
и трафик потечет уже по этому пути.