title: route-map

# Особенности применения "route-map continue"

Disclaimer:
**Иногда хочется странного...**

## Вот такая задача появилась: 
## Начальные условия:
Имеется локация с несимметричными по полосе каналами на которых есть ECMP.
Данная локация имеет свою AS.
По нагрузке один из каналов с меньшей полосой, при прочих равных условиях начинает достигать свего максимума по полосе

## Задача:
- Снять часть трафика с данного канала.

## Решение:
Сделать из него UCMP чз BGP не получается по техническим условиям.
Как вариант разбалансить можно принудительным выставлением доп. prepend-a для "more specific" префиксов (больших /23)

**Решить данную задачу можно через route-map**

```bash
!Настройка Дистрибуции в локации
!
ip prefix-list CORE-OUT seq 5 permit 192.168.0.0/23 le 24
ip prefix-list CORE-OUT seq 10 permit 192.168.10.0/24 le 32
ip prefix-list CORE-OUT seq 15 permit 172.24.128.0/17 le 24
!
route-map RM-OUT permit 10
 match ip address prefix-list PL-GE-24
 set as-path prepend 1111
 continue 20
route-map RM-OUT permit 20
 match ip address prefix-list CORE-OUT
route-map RM-OUT deny 30
!
router bgp 11111
  neighbor 2.2.2.2 remote-as 22222
  !
  address-family ipv4
   neighbor 2.2.2.2 route-map RM-OUT out
 
ip prefix-list PL-GE-24 seq 10 permit 0.0.0.0/0 ge 24
```

## Логика работы route-map RM-OUT:
### 1. Приходит prefix, соответствующий prefix-list CORE-OUT с маской от 17-24
например 172.24.128.0/17
```bash
  1.1. Согласно правилу в route-map RM-OUT permit 10 
       префикс 172.24.128.0/17 не подпадает под его условия 
       и переходит к след prepend
  1.2. Переходит к следующему правилу в route-map RM-OUT permit 20 
       префикс 172.24.128.0/17 подпадает под него у будет "сматчен" 
       и пройдет на выход
  ИТОГО префикс попал без изменений в advertised-route    
```

### 2. Приходит prefix, соостветствующий prefix-list CORE-OUT, с маской от 24 до 32
например 172.24.128.0/24
```bash    
   1.1. Согласно правилу в route-map RM-OUT permit 10 
       префикс 172.24.128.0/24 будет "сматчен", 
       на него будет установлен доп.препенд,
       и согласно правилу "continue 20" данный префикс перейдет 
       в route-map RM-OUT permit 20
    1.2. Согласно правилу в route-map RM-OUT permit 20 
       префикс 172.24.128.0/24 подпадает под него у будет "сматчен" 
       и пройдет на выход
  ИТОГО префикс попал с с доп.препендом в advertised-route    
```

### 3. Приходит prefix, несоответствующий prefix-list CORE-OUT например с маской 24 до 32
например 192.168.4.0/30
```bash
	1.1. Согласно правилу в route-map RM-OUT permit 10 
       префикс 192.168.4.0/30 будет "сматчен", 
       на него будет установлен доп.препенд,
       и согласно правилу "continue 20" данный префикс перейдет 
       в route-map RM-OUT permit 20
    1.2. Согласно правилу в route-map RM-OUT permit 20 
       префикс 172.24.128.0/24 несоответствует правилу 
       и переходит на route-map RM-OUT deny 30 
       где отбрасывается
  ИТОГО префикс не попал в advertised-route    
```
## ПО ИТОГУ
мы решили задачу расбаланса трафика с выставлением доп. препенда для "more specific" префиксов,
т.о. трафик к "more specific" префиксаим будет идти через более широкий канал...

!!! warning "Важно"
	**При использовании "continue" правило по умолчанию у route-map меняется с "implicit deny" на "implicit permit", п.э. чтобы работало как в нашем случае необходимо в явном виде указывать
	последним правилом "deny"**

Cсылка на документацию Cisco:
[Once a route is matched in a route map permit entry with a continue command clause, it will not be processed by the implicit deny at the end of the route-map.](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/iproute_bgp/configuration/xe-3s/irg-xe-3s-book/bgp-route-map-continue.html#GUID-9B0DBD01-A010-4280-B9F4-765DE09B5D55)
