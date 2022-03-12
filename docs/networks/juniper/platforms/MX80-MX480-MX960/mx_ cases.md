title: MX cases

# Кейсы по MX80/MX480/MX960

## MPLS

### L2VPN
#### local-l2circuit

## Настройка local-switch l2circuit
Для организации L2 связности между двумя интерфейсами на маршрутизаторах MX 
используется так называемый "local-switch l2circuit", по смыслу функционала аналог bridge-domain-a


![local-l2circuit](img/local-l2circuit.jpg)


```bash
"Пример настройки local-switch l2circuit"

set interfaces xe-1/1/1 unit 333 description "L2VPN; local-switch l2circuit"
set interfaces xe-1/1/1 unit 333 encapsulation vlan-ccc
set interfaces xe-1/1/1 unit 333 vlan-id 333
set interfaces xe-1/1/1 unit 333 family ccc policer input 150Mbit

set interfaces ae2 unit 333 description "L2VPN; local-switch l2circuit"
set interfaces ae2 unit 333 encapsulation vlan-ccc
set interfaces ae2 unit 333 vlan-id 333
set interfaces ae2 unit 333 family ccc policer input 150Mbit

"Для связки двух интерфейсов используется конструкция,"
"в которой указывается начальный и конечный иньерфейс локольного l2circuit"
set protocols l2circuit local-switching interface xe-1/1/1.333 end-interface interface ae2.333

set protocols l2circuit local-switching interface xe-1/1/1.333 description "L2VPN; local-switch l2circuit"
set protocols l2circuit local-switching interface xe-1/1/1.333 ignore-mtu-mismatch

set firewall policer 150Mbit if-exceeding bandwidth-limit 150m
set firewall policer 150Mbit if-exceeding burst-size-limit 14400000
set firewall policer 150Mbit then discard
```

#### l2circuit
#### l2circuit + backup

#### vpls Martini mode
#### vpls Kompella mode
#### vpls Kompella mode + mesh group

### EVPN


```bash
```