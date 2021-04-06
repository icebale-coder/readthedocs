
title: QinQ Dlink

# Настройка QinQ base 
на Dlink DGS-1100-06/ME

## Базовая терминология
!!! warning "Discrambler"
	почему господа из Dlink Нигде по человечески не описали логику работы ролей NNI и UNI? п.э. опишу своими словами

"Доки QinQ"

[ссылка1](https://www.dlink.ru/ru/faq/62/1641.html)
[ссылка2](ftp://ftp.dlink.ru/pub/Trainings/SwitchWhitePapers/Q-in-Q_Port-Based_and_Selective.pdf)

### Роли портов UNI, NNI

#### UNI  
**Режим работы UNI:** при включении pvid на порту в данном режиме на весь трафик, попадающий в коммутатор с данного порта сверху набрасывается еще один тег (pvid)"

*Логика работы UNI:*
```bash
 1. Для входящего на порт трафика:
    При поступлении кадров на порт на них принудительно навешивается сверху 
    еще один тег (номер pvid)
 2. Для исходящего с порта трафика:
    Верхний тег (номер pvid) отбрасывается из заголовка кадра и дальше передается 
    в стандартном виде dot1.Q
```

#### NNI
**Режим работы NNI:** стандартый режим работы порта.

**Вид пакета с двойным тегом**
![image-QinQ-wireshark](../img/QinQ-wireshark.jpg)

### Настройка QinQ на примере данной схемы
![image-Dlink-QinQ](../img/Dlink-QinQ-schema.jpg)

#### Словесное описание схемы и задачи:

**Задача: соединить 2 офиса между собой через сеть провайдера, используя технологию QinQ на сети провайдера, по сети провайдера кадры данного клиента передаются в 1000-ом влане.**

1. SW3, SW4 - по сути выполняют роль коммутаторов доступа, которые работают в   
   стандартном режиме, порты со стороны клиента нетегированные (в access-е), порт аплинка p4 - тегированные (в trunk-е).
2. SW1, SW2 - выполняют роль коммутаторов в сети провайдера.
   Соответственно на порту p4 SW1/2 происходит добавление и удаление дополнительного тега.


#### Настройки SW3/SW4
SW3/SW4 - обычные коммутаторы - на них самые обычные настройки
```bash
# Создаем клиентские вланы
create vlan 2101 tag 2101
create vlan 2102 tag 2102
create vlan 2103 tag 2103

# Удаляем дефолтовый влан
config vlan vlanid 1 delete 1-6

# Нетегированные вланы в сторону клиентов
config vlan vlanid 2101 add untagged 1
config vlan vlanid 2102 add untagged 2
config vlan vlanid 2103 add untagged 3

# Тегированные вланы в сторону аплинка
config vlan vlanid 2101 add tagged 4
config vlan vlanid 2102 add tagged 4
config vlan vlanid 2103 add tagged 4

#  Q-in-Q выключен (по умолчанию)
disable qinq
```
#### Настройки SW1/SW2
Коммутаторы сети провайдера, на них настроен механизм QinQ

```bash
# Создаем QinQ влан
create vlan 1000 tag 1000
# Создаем клиентские вланы
create vlan 2101 tag 2101
create vlan 2102 tag 2102
create vlan 2103 tag 2103

# Удаляем дефолтовый влан
config vlan vlanid 1 delete 1-6

# Тегированный влан 1000 в сторону SW1--p1<--->p1--SW2
config vlan vlanid 1000 add tagged 1

# Настройка порта 4, который работает в режиме UNI
config vlan vlanid 1000 add untagged 4
config vlan vlanid 2101 add tagged 4
config vlan vlanid 2102 add tagged 4
config vlan vlanid 2103 add tagged 4
config port_vlan 4 pvid 1000

# Включаем QinQ
# стандартный type id 0x8100 в заголовке тега QinQ
enable qinq
config qinq ports 1-3,5-6 role nni outer_tpid 0x8100
config qinq ports 4 role uni outer_tpid 0x8100
```
