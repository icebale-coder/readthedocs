title: Frame Ethernet


# Анатомия кадра Ethernet

Попробую описать как это выглядет с указанием уровней OSI

## 7ми уровневая модель OSI
![osi](img/osi.jpg)


## Ethernet II
```bash
14 bytes = DMAC(6) + SMAC(6) + type(2)  - заголовок кадра  (L2)
+
20 bytes = ...+ SA + DA +... - заголовок пакета (L3)
+
Если у нас tcp,upd,icmp
20 bytes - заголовок сегмента (L4) 
+
payload - L4 - L7 
```

### L2
Заголовок кадра может быть увеличен за счет использования vlan

![frame-header](img/frame-header.jpg)

#### VLAN
Стандарт 802.1Q — добавляет 4 байта

![frame-header+dot1.q](img/802.1q.jpg)


### L3
20 bytes - заголовок пакета (L3)

![ip-header](img/ip-header.jpg)

	
### L4-L7
Payload
[xxxx] bytes - payload (L4-L7)


## Пример icmp протокола:
### Echo-Request

![frame](img/icmp-echo-request.jpg)

#### L2
```java
 type: 0x800 - IPv4
```

![ip-header](img/icmp-echo-request-l2.jpg)

#### L3

```java
 protocol: 1 - ICMP
```

![segment-header](img/icmp-echo-request-l3.jpg)

#### L4-L7
```java
Type: 8  - Echo ping (request)
Code: 0
```

![icmp-request](img/icmp-echo-request-l4-l7.jpg)


### Echo-Reply
![ip-header](img/icmp-echo-reply.jpg)

#### L2
```java
 type: 0x800 - IPv4
```

![ip-header](img/icmp-echo-reply-l2.jpg)

#### L3
```java
 protocol: 1 - ICMP
```
![ip-header](img/icmp-echo-reply-l3.jpg)

#### L4-L7
```java
Type: 0  - Echo ping (reply)
Code: 0
```

![ip-header](img/icmp-echo-reply-l4-l7.jpg)

Пример icmp дампа в wireshark можно посмотреть [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/icmp-ping.pcapng)