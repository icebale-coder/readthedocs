title: EX cases

# Кейсы по EX

## Настройка L3 интерфейса на коммутаторе

```bash
"L3 интерфейс(SVI aka cisco) на ex коммутаторах, например на ex4500-40f"
"Создаем vlan 1000"
"Создаем L3 интерфейс в 1000-ом влане"
"Прописываем влан на интерфейсах"

    set vlans VL1000 description Test1000
    set vlans VL1000 vlan-id 1000
    set vlans VL1000 l3-interface vlan.1000

    set interfaces vlan unit 1000 description Test1000
    set interfaces vlan unit 1000 family inet address 172.16.164.211/29

    set interfaces xe-0/0/1 unit 0 family ethernet-switching vlan members VL1000
    set interfaces ae0 unit 0 family ethernet-switching vlan members VL1000
```