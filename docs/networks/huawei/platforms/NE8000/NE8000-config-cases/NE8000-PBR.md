title: NE8000 PBR

# Huawei PBR

Задача настройки PBR подобно настройке [Cisco PBR](https://icebale.readthedocs.io/en/latest/networks/cisco/Cisco.cases/NXOS-PBR/)

## 1. На интерфейсе указывается traffic-policy 
На интерфейс в сторону трафика, для которого надо делать PBR вешается полисер
в данном случае для входящего на интерфейс трафика
**traffic-policy TO-NAT inbound**
```bash
interface Eth-Trunk10.100
 vlan-type dot1q 100
 description << DS100 >>
 ip address 192.168.22.1 255.255.255.254
 traffic-policy TO-NAT inbound
```
## 2. traffic policy
Трафик полисер обращается к классификатору
```bash
traffic policy TO-NAT
 share-mode
 classifier TC-NAT0 behavior TB-NAT0 precedence 100
 classifier TC-NAT1 behavior TB-NAT1 precedence 110
```

## 3. traffic classifier
Классификатор в свою очередь сравнивает ip адреса источника пакета с ACL
```bash
traffic classifier TC-NAT0 operator or
 if-match acl name ACL-NAT0
#
traffic classifier TC-NAT1 operator or
 if-match acl name ACL-NAT1
```

## 4. acl
ACL по source ip адресам, для которых надо применить перенаправления трафика
```bash
acl name ACL-NAT1 basic
 rule 10 permit source 172.0.0.0 0.0.0.255
#
acl name ACL-NAT2 basic
 rule 10 permit source 172.0.1.0 0.0.0.255
#
```

## 5. traffic behavior 
traffic behavior - это аналог Track в Cisco.
Отвечает за перенаправление пакета на nexthop в соответствии с доступностью этого некстхопа.

**Логика следующая:**

**- если адрес nexthop-a первого по порядку доступен, то перенаправляем пакет на него,**

**- если адрес nexthop-a первого по порядку недоступен, то переходим к следующему доступному по порядку некстхопу**

**Это определяется с помощью nqa - это аналог Cisco SLA**

```bash
traffic behavior TB-NAT0
 redirect ipv4-multinhp nhp 192.168.1.1 nqa NATS NQA_NAT1 nhp 192.168.1.2 nqa NATS NQA_NAT2 routing-filter default-routing pri-type common
#
traffic behavior TB-NAT1
 redirect ipv4-multinhp nhp 192.168.1.2 nqa NATS NQA_NAT2 nhp 192.168.1.1 nqa NATS NQA_NAT1 routing-filter default-routing pri-type common
#
```

## 6. nqa 
**nqa** - это аналог **Сisco SLA**, который в данном случае отслеживает доступность ip адресов NAT-ов по icmp
```bash
nqa test-instance NATS NQA_NAT1
 test-type icmp
 description !! Test availability of NAT1 !!
 destination-address ipv4 192.168.1.1
 interval seconds 1
 timeout 1
 probe-count 2
 source-interface LoopBack0
 frequency 3
 start now
#
nqa test-instance NATS NQA_NAT2
 test-type icmp
 description !! Test availability of NAT2 !!
 destination-address ipv4 192.168.1.2
 interval seconds 1
 timeout 1
 probe-count 2
 source-interface LoopBack0
 frequency 3
 start now
#
```

!!! warning "Важно"
	Для того, чтобы сделать редирект для пакета, у которого по RIB некстхоп это дефолт,
	то необходимо глобально разрешить редирект для дефолта.
	**Без этой настройки редиректа для дефолтовой записи в RIB НЕ БУДЕТ,**
	**соответственно не будет работать и сам PBR для дефолта,**
	**в таком случае PBR будет работать только для явных маршрутов в RIB**
 
```bash
slot 1          
 redirect routing-filter default-routing
```