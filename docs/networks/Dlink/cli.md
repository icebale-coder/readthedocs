title: cli Dlink

# Базовые команды cli Dlink
Мне как адепту и олдфагу веб морбы Dlink-а они очень как нужны )

## Просмотр информации коммутаторе (ip адрес, место установки , версия ПО...) 
```bash
show switch
```

## Просмотр описания портов
```bash
sh ports desc
```

## Просмотр конфига

```bash
show config curr
```

## Просмотр логов

```bash
show log
```

## Проcмотр fdb

```bash
show fdb
```

### Проcмотр fdb по влану 

```bash
show fdb vlan 1
```

### Проcмотр fdb по порту 

```bash
show fdb port 1
```

## Проcмотр вланов на порту 

```bash

show vlan ports 4
Command: show vlan ports 4


Port 4
VLAN ID  Untagged  Tagged
-------  --------  ------
  657       X        -
 2100       -        X
 2101       -        X
```

## Проcмотр влана по номеру (на каких портах данный влан присутствует)

```bash
show vlan vlanid 10
Command: show vlan vlanid 10

VID                : 10        VLAN NAME      : manager
VLAN Type          : Static
Member Ports       : 5-6
Tagged Ports       : 5-6
Untagged Ports     :
```

## Проcмотр влана по имени (на каких портах данный влан присутствует)

```bash
show vlan manager
Command: show vlan manager

VID                : 10        VLAN NAME      : manager
VLAN Type          : Static
Member Ports       : 5-6
Tagged Ports       : 5-6
Untagged Ports     :
```

## Добавить новый влан 
```bash
# создание влана 2101 с именем NAME_2101
create vlan NAME_2101 tag 2101
```

## Добавить влан на определенный порт
```bash
# Добавление влана 2101 на 4ый порт в теге
config vlan vlanid 2101 add tagged 4

# Добавление влана 2101 на 5ый порт в теге
config vlan vlanid 2101 add untagged 5
```

## Удалить влан на определенном порту
```bash
# удалить влан 657 на порту 1
config vlan 657 delete 1
```
