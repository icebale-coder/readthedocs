title: BGP

# Протокол BGP

## Определение/Назначение

## Настройки
### ORF  
**orf - output route filters**
```bash

```
  Механизм, который позволяет посылать соседу информацию о тех префиксах, которые хочет принимать вторая сторона.
  Удобно в случае, когда идет фильтрация входящих префиксов. При включенном orf с обоих сторон происходит обмен фильтрами на входящие префиксы и сосед заранее при отправке префиксов сделает на своей стороне фильтрацию лишних (не нужных соседу) и отправит только необходимые.

#### Реализация на оборудовании

##### Cisco
**IOS/IOS-XE**

```bash
     router bgp 11111
      address-family ipv4
        neighbor 1.1.1.1 capability orf prefix-list both
```
подробнее см. [документацию](http://admindoc.ru/1250/bgp-outbound-route-filtering/)

**IOS-XR**
В IOS-XR надо в явном виде указывать prefix-set для ORF

```bash
  prefix-set orf-preset
    172.16.1.0/24,
    172.16.5.0/24,
    172.16.11.0/24
  end-set
  
  route-policy policy-orf
    if orf prefix in orf-preset then
      drop
    endif
    if orf prefix in (172.16.3.0/24, 172.16.7.0/24, 172.16.13.0/24) then
      pass
    endif
  
  router bgp 2
    neighbor 1.1.1.1
      remote-as 3
      address-family ipv4 unicast
        orf route-policy policy-orf
```

подробнее см. [документацию](https://www.cisco.com/c/en/us/td/docs/routers/xr12000/software/xr12k_r4-0/routing/configuration/guide/rc40xr12k_chapter7.html#con_1206744)

##### Juniper
**MX80**

```bash
neighbor 2.2.2.2 {
    description "== NEIGHBOR with ORF ==";
    local-address 1.1.1.1;
    peer-as 22222;
    outbound-route-filter {
        bgp-orf-cisco-mode;
        prefix-based {
            accept {
                inet;
            }
        }
    }
}
```

!!! note "Примечание"
	С данной настройкой не удалось подружить Джун с Хуавеем. Нужно сделать лабу

подробнее см. [документацию](https://www.juniper.net/documentation/en_US/junos/topics/topic-map/basic-routing-policies.html#id-example-configuring-bgp-prefix-based-outbound-route-filtering)

##### Huawei
**NE8000-F1A**

```bash
    bgp 11111
     ipv4-family unicast
      peer 1.1.1.1 capability-advertise orf ip-prefix both
```
подробнее см. [документацию](https://support.huawei.com/enterprise/en/doc/EDOC1100146591/ec511dde/configuring-prefix-based-bgp-orf)


### Soft-reconfiguration  
**soft-reconfiguration** 
```bash

```
  Механизм, который позволяет хранить локально таблицу префиксов, принятую от соседа.
  Удобно использовать данный механизм при изменении входящего фильтра, тогда фильтр применяется уже к актуальной существующей таблице префиксов и нет необходимость перезапрашивать всю таблицу заново.\
  Полезно при принятии FullView
  Недостаток: необходима доп. память, чтобы храннить таблицу префиксов

Общая схема работы 
![image-bgp-rib](img/bgp-rib.jpg)

подробнее см. [документацию](https://networklessons.com/bgp/bgp-soft-reset-reconfiguration)


!!! note "Замечание"
    Крайне нежелательно использовать soft-reconfiguration на оборудовании с недостаточным количеством памяти

#### Реализация на оборудовании
##### Cisco

**IOS/IOS-XE**

!!! note "Примечание"
    По умолчанию не включена


Включить можно командой 

```bash
 router bgp 50022
  neighbor 1.1.1.1 remote-as 31257
  neighbor 1.1.1.1 description == NEIGHBOR 1 ==
  !
  address-family ipv4
   neighbor 1.1.1.1 activate
   neighbor 1.1.1.1 soft-reconfiguration inbound
```

**IOS-XR**

!!! note "Примечание"
    По умолчанию не включена

Включить можно командой 

```bash
 neighbor 1.1.1.1
  remote-as 11111
  timers 60 180
  description ==== NEIGHBOR 1 ====
  bmp-activate server 1
  update-source Bundle-Ether1.1
  address-family ipv4 unicast
   send-community-ebgp
   route-policy POLICY_IN in
   route-policy POLICY_OUT out
   soft-reconfiguration inbound always
```
подробнее см. [документацию](https://www.cisco.com/c/en/us/td/docs/routers/crs/software/crs_r3-9/routing/command/reference/rr39crs1book_chapter1.html#wp307968196)

##### Juniper
**MX80**

!!! note "Примечание"
     По умолчанию (не отображается в конфиге) включен 
     в таком режиме - хранит всю информацию о маршрутах, 
     полученную от BGP, за исключением маршрутов, 
     путь AS которых является зацикленным и цикл которых включает локальную AS.

Выключить можно командой 
```bash
 neighbor 1.1.1.1 {
    description "== NEIGHBOR 1 ==";
    local-address 2.2.2.2;
    keep none;
 }
```
подробнее см. [документацию](https://www.juniper.net/documentation/en_US/junos/topics/reference/configuration-statement/keep-edit-protocols-bgp.html)

##### Huawei
**NE8000-F1A**

!!! note "Примечание"
     По умолчанию хранит все маршруты которые прошли через фильтрацию import

Включить можно командой

```bash
    bgp 11111
     ipv4-family unicast
      peer 1.1.1.1 keep-all-routes
```
подробнее см. [документацию](https://support.huawei.com/enterprise/en/doc/EDOC1000174069/78fc123/configuring-bgp-soft-reset)

### PMTUD
PMTUD - Path MTU Discovery позволяет увеличить скорость принятия анонсов по BGP, например при приеме или отправки FullView

Для увеличения скорости, а по сути в данном контексте под скоростью подразумевается размер MTU и в конечном счете значение MSS в tcp сессии используется механизм [PMTUD](https://icebale.readthedocs.io/en/latest/networks/protocols/TCP/#path_mtu_discovery_pmtud)

Для этого нужно:
1. Максимально увеличить MTU на интерфейсе в сторону соседа
2. Включить Path-MTU-Discovery


#### Juniper [mtu-discovery]

[Изначально при установлении tcp соединения по порту 179 для BGP по дефолту MSS устанавливается равным 512 byte. Это делается для того, чтобы минимизировать возможность фрагментации пакета при прохождении промежуточных маршрутизаторов, где возможно выставлен меньший MTU.](https://www.juniper.net/documentation/us/en/software/junos/bgp/topics/ref/statement/mtu-discovery-edit-protocols-bgp.html)

У Джунипера по умолчанию PMTUD [включен](https://www.juniper.net/documentation/us/en/software/junos/bgp/topics/ref/statement/mtu-discovery-edit-protocols-bgp.html)
```bash
system internet-options{
   mtu-discovery
}
interfaces {
  ae0 {
      mtu 9000;
  }
}
```


#### Cisco [tcp path-mtu-discovery]
У Циско по умолчагнию PMTUD [включен](https://www.cisco.com/c/dam/en/us/td/docs/routers/asr920/configuration/guide/iproute/16-8-1/b-iproute-xe-16-8-1-asr920.html)
```bash
tcp path-mtu-discovery

interface Bundle-Ether1.1111
  ! Размер MTU нужно устанавливать уже непосредственно на интерфейсах/сабинтерфейсах
  mtu 9212
```


#### Huawei [path-mtu auto-discovery]
У Хуавея по умолчанию PMTUD [выключен](https://support.huawei.com/enterprise/en/doc/EDOC1100058917/dd638600/configuring-path-mtu-auto-discovery)
```bash
# MTU выставляется непосредственно на интерфейсе или сабинтерфейсе
interface Eth-Trunk19
 mtu 9216

bgp 11111
 peer 2.2.2.2 path-mtu auto-discovery
```