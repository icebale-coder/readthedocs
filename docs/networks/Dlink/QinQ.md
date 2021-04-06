
title: QinQ Dlink

# Настройка QinQ на Dlink

## Базовая терминология
!!! warning "Discrambler"
	почему господа из Dlink Нигде по человечески не описали логику работы ролей NNI и UNI?

"Доки Q-inQ"
[тыц1](https://www.dlink.ru/ru/faq/62/1641.html)
[тыц2](ftp://ftp.dlink.ru/pub/Trainings/SwitchWhitePapers/Q-in-Q_Port-Based_and_Selective.pdf)


### Роли портов UNI, NNI

#### UNI
 	при включении pvid на порту в данном режиме на весь трафик, попадающий на коммутатор с данного порта сверху набрасывается еще один тег (pvid)

```bash
 Логика работы UNI:
 1. Для входящего на порт трафика:
    При поступлении кадров на порт на них принудительно навешивается сверху еще один     тег  (номер pvid)
 2. Для исходящего с порта трафика:
    Верхний тег (номер pvid) отбрасывается из заголовка кадра и дальше передается в стандартном виде dot1.Q
```

#### NNI
 стандартый режим работы порта.

### Настройка QinQ на примере данной схемы
![image-Dlink-QinQ](../img/Dlink-QinQ-schema.jpg)


Имеется 
назначаем TPID 0x8100 (как у 802.1Q) чтобы не потерять управление коммутатором после включения Q-in-Q
+config qinq ports 1-6 outer_tpid 0x8100

# 

-config vlan 2100 delete  4
#config vlan 2101 delete  4
+config vlan 657 delete 4

#
create vlan 1000 tag 1000
config vlan 1000 add tagged 1

# переводим в режим работы UNI
+config qinq ports 1 role uni


create vlan_translation ports 4 cvid 2100 replace svid 1000

# включаем Q-in-Q
enable qinq

config vlan 1000 add untagged 4
config gvrp 4 pvid  1000

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
1. если порт находится в NNI - то он продолжает работать при включении QinQ
