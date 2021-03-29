title: Dlink cli base

# Базовые команды cli
Мне как адепту и олдфагу веб морбы Dlink-а они очень как нужны )

## Просмотр конфига

```bash
show config curr
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

### Проcмотр вланов на порту 

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

### Проcмотр влана по номеру (на каких портах данный влан присутствует)

```bash
show vlan vlanid 10
Command: show vlan vlanid 10

VID                : 10        VLAN NAME      : manager
VLAN Type          : Static
Member Ports       : 5-6
Tagged Ports       : 5-6
Untagged Ports     :
```

### Проcмотр влана по имени (на каких портах данный влан присутствует)

```bash
show vlan manager
Command: show vlan manager

VID                : 10        VLAN NAME      : manager
VLAN Type          : Static
Member Ports       : 5-6
Tagged Ports       : 5-6
Untagged Ports     :
```
