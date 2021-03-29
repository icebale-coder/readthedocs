title: cli NE8000-F1A

# Базовые команды cli Huawei 
!!! warning "Важно!"
		На маршрутизаторах Huawei используется система commit-а
		что позволяет проверять набор изменений перед применением их в рабочий конфиг.

## Просмотр конфига

```bash
dis cur conf
```

## Просмотр конфига интерфейса NE8000-F1A

```bash
dis cur int XGigabitEthernet 0/0/24
#
interface XGigabitEthernet0/0/24
 description << test >>
 port link-type trunk
 port trunk allow-pass vlan 10 20 to 25
 dhcp snooping trusted

```

