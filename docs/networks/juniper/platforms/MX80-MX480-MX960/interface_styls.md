title: Enterprise vs Provider

# Enterprise style vs Provider style 
В джунипере есть 2 стиля описания интерфейсов: Enterprise style и Provider style 


## Enterprise style
```bash
  MX> show configuration | display set | match 2000                
    set interfaces xe-1/0/1 unit 2000 description "L2VPN"
    set interfaces xe-1/0/1 unit 2000 family bridge policer input 100Mbit
    set interfaces xe-1/0/1 unit 2000 family bridge interface-mode trunk
    set interfaces xe-1/0/1 unit 2000 family bridge vlan-id-list 2000-2001

    set interfaces ge-2/2/2 unit 2000 description "L2VPN"
    set interfaces ge-2/2/2 unit 2000 family bridge policer input 100Mbit
    set interfaces ge-2/2/2 unit 2000 family bridge interface-mode trunk
    set interfaces ge-2/2/2 unit 2000 family bridge vlan-id-list 2000-2001

    set interfaces ae1 unit 2000 description "L2VPN"
    set interfaces ae1 unit 2000 family bridge policer input 100Mbit
    set interfaces ae1 unit 2000 family bridge interface-mode trunk
    set interfaces ae1 unit 2000 family bridge vlan-id-list 2000-2001

    set routing-instances Local bridge-domains VL2000 description "L2VPN"
    set routing-instances Local bridge-domains VL2000 vlan-id-list 2000-2001
    set routing-instances Local interface xe-1/0/1.2000
    set routing-instances Local interface ge-2/2/2.2000
    set routing-instances Local interface ae1.2000
```

￼
!Логичнее было бы каждый влан засунуть в свой сабик. Обединение в один юнит просто сокращает конфиг (при этом усложня
### Diagnostic
```bash
"MX> show bridge mac-table instance Local | match 2000"
  Bridging domain : VL2000-vlan-2000, VLAN : 2000
    11:65:92:fa:a6:c0   D        xe-1/0/1.2000   
    48:12:12:19:12:cd   D        ge-2/2/2.2000  
    10:10:ca:46:a5:e7   D        ge-2/2/2.2000  
    11:11:6a:27:00:40   D        xe-1/0/1.2000   
    12:12:9f:43:69:2f   D        xe-1/0/1.2000   
    00:00:f3:65:ea:0c   D        xe-1/0/1.2000   

  Bridging domain : VL2000-vlan-2001, VLAN : 2001
    11:65:92:fa:a6:c0   D        ae1.2000        
    48:12:12:19:12:cd   D        ge-2/2/2.2000  
    10:10:ca:46:a5:e8   D        ge-2/2/2.2000  
    11:11:6a:27:00:40   D        ae1.2000        
    12:12:9f:43:69:30   D        ae1.2000        
    00:00:f3:65:ea:34   D        ae1.2000     
```

либо по отдельности

```bash    
"MX> show bridge mac-table instance Local bridge-domain VL2000-vlan-2000"
  MAC flags       (S -static MAC, D -dynamic MAC, L -locall
￼
!Логичнее было бы каждый влан засунуть в свой сабик. Обединение в один юнит просто сокращает конфиг (при этом усложняy learned, C -Control MAC
      O -OVSDB MAC, SE -Statistics enabled, NM -Non configured MAC, R -Remote PE MAC, P -Pinned MAC)

  Routing instance : Local
  Bridging domain : VL2000-vlan-2000, VLAN : 2000
    MAC                 MAC      Logical          NH     MAC         active
    address             flags    interface        Index  property    source
    11:65:92:fa:a6:c0   D        xe-1/0/1.2000   
    48:12:12:19:12:cd   D        ge-2/2/2.2000  
    10:10:ca:46:a5:e7   D        ge-2/2/2.2000  
    11:11:6a:27:00:40   D        xe-1/0/1.2000   
    12:12:9f:43:69:2f   D        xe-1/0/1.2000   
    00:00:f3:65:ea:0c   D        xe-1/0/1.2000   

"MX> show bridge mac-table instance Local bridge-domain VL2000-vlan-2001"
  MAC flags       (S -static MAC, D -dynamic MAC, L -locally learned, C -Control MAC
      O -OVSDB MAC, SE -Statistics enabled, NM -Non configured MAC, R -Remote PE MAC, P -Pinned MAC)

  Routing instance : Local
  Bridging domain : VL2000-vlan-2001, VLAN : 2001
    MAC                 MAC      Logical          NH     MAC         active
    address             flags    interface        Index  property    source
    11:65:92:fa:a6:c0   D        ae1.2000        
    48:12:12:19:12:cd   D        ge-2/2/2.2000  
    10:10:ca:46:a5:e8   D        ge-2/2/2.2000  
    11:11:6a:27:00:40   D        ae1.2000        
    12:12:9f:43:69:30   D        ae1.2000        
    00:00:f3:65:ea:34   D        ae1.2000        
```

```bash
"Описание прозой"
  По факту имеются 3 интерфейса (xe-1/0/1, ge-2/2/2, ae1) на каждом из них присутствет 2 влана (2000,2001)
  для объединения их в бридж домене в enterprise стиле нужно 
  в одном и том же сабинтерфейсе прописать vlan-id-list.
  Также в RI сабинтерфейс добавляется не в бридж-домен, а просто в интерфейсы RI.
  И таким образом по сути будет получаться, что 
  "В ОДНОМ САБИНТЕРФЕЙСЕ МОЖЕТ ПРИСУТСТВОВАТЬ СРАЗУ НЕСКОЛЬКО ВЛАНОВ" 
  - ТАКОЙ ФОКУС в cisco или huawei не удастся!!!
  "set interfaces ae1 unit 2000 family bridge interface-mode trunk" - "как тебе такое Илон Маск (ц)"
```

```bash
!Логичнее было бы каждый влан засунуть в свой сабик. Обединение в один юнит просто сокращает конфиг (при этом усложняя логику понимания)...
Тогда конфиг стал бы таким:

  set interfaces ge-2/2/2 unit 2000 description "L2VPN"
  set interfaces ge-2/2/2 unit 2000 family bridge policer input 100Mbit
  set interfaces ge-2/2/2 unit 2000 family bridge interface-mode trunk
  set interfaces ge-2/2/2 unit 2000 family bridge vlan-id-list 2000

  set interfaces ae1 unit 2000 description "L2VPN"
  set interfaces ae1 unit 2000 family bridge policer input 100Mbit
  set interfaces ae1 unit 2000 family bridge interface-mode trunk
  set interfaces ae1 unit 2000 family bridge vlan-id-list 2000

  set interfaces xe-1/0/1 unit 2001 description "L2VPN"
  set interfaces xe-1/0/1 unit 2001 family bridge policer input 100Mbit
  set interfaces xe-1/0/1 unit 2001 family bridge interface-mode trunk
  set interfaces xe-1/0/1 unit 2001 family bridge vlan-id-list 2001

  set interfaces ge-2/2/2 unit 2001 description "L2VPN"
  set interfaces ge-2/2/2 unit 2001 family bridge policer input 100Mbit
  set interfaces ge-2/2/2 unit 2001 family bridge interface-mode trunk
  set interfaces ge-2/2/2 unit 2001 family bridge vlan-id-list 2001

  set interfaces ae1 unit 2001 description "L2VPN"
  set interfaces ae1 unit 2001 family bridge policer input 100Mbit
  set interfaces ae1 unit 2001 family bridge interface-mode trunk
  set interfaces ae1 unit 2001 family bridge vlan-id-list 2001

  set routing-instances Local bridge-domains VL2000 description "L2VPN"
  set routing-instances Local bridge-domains VL2000 vlan-id-list 2000
  set routing-instances Local interface xe-1/0/1.2000
  set routing-instances Local interface ge-2/2/2.2000
  set routing-instances Local interface ae1.2000

  set routing-instances Local bridge-domains VL2001 descr
￼
!Логичнее было бы каждый влан засунуть в свой сабик. Обединение в один юнит просто сокращает конфиг (при этом усложняiption "L2VPN"
  set routing-instances Local bridge-domains VL2001 vlan-id-list 2001
  set routing-instances Local interface xe-1/0/1.2001
  set routing-instances Local interface ge-2/2/2.2001
  set routing-instances Local interface ae1.2001
```

## Provider style 
 Провайдерский стиль более удобоваримо читается в конфиге:

```bash
"MX> show configuration | display set | match 2000"              
  
  !vlan 2000
  set interfaces xe-1/0/1 unit 2000 description "L2VPN"
  set interfaces xe-1/0/1 unit 2000 family bridge policer input 100Mbit
  set interfaces xe-1/0/1 unit 2000 family bridge vlan-id 2000

  set interfaces ge-2/2/2 unit 2000 description "L2VPN"
  set interfaces ge-2/2/2 unit 2000 family bridge policer input 100Mbit
  set interfaces ge-2/2/2 unit 2000 family bridge vlan-id 2000

  set interfaces ae1 unit 2000 description "L2VPN"
  set interfaces ae1 unit 2000 family bridge policer input 100Mbit
  set interfaces ae1 unit 2000 family bridge vlan-id 2000

  set routing-instances Local bridge-domains VL2000 description "L2VPN"
  set routing-instances Local bridge-domains VL2000 vlan-id 2000
  set routing-instances Local bridge-domains VL2000 interface xe-1/0/1.2000
  set routing-instances Local bridge-domains VL2000 interface ge-2/2/2.2000
  set routing-instances Local bridge-domains VL2000 interface ae1.2000

"MX> show configuration | display set | match 2001"                

  !vlan 2001
  set interfaces xe-1/0/1 unit 2001 description "L2VPN"
  set interfaces ae1 unit 2179 encapsulation vlan-bridge
  set interfaces xe-1/0/1 unit 2001 family bridge policer input 100Mbit
  set interfaces xe-1/0/1 unit 2001 family bridge vlan-id 2001

  set interfaces ge-2/2/2 unit 2001 description "L2VPN"
  set interfaces ge-2/2/2 unit 2001 family bridge policer input 100Mbit
  set interfaces ge-2/2/2 unit 2001 family bridge vlan-id 2001

  set interfaces ae1 unit 2001 description "L2VPN"
  set interfaces ae1 unit 2001 family bridge policer input 100Mbit
  set interfaces ae1 unit 2001 family bridge vlan-id 2001
  set interfaces ae1 unit 2179 encapsulation vlan-bridge  

  set routing-instances Local bridge-domains VL2001 description "L2VPN"
  set routing-instances Local bridge-domains VL2001 vlan-id 2001
  set routing-instances Local bridge-domains VL2001 interface xe-1/0/1.2001
  set routing-instances Local bridge-domains VL2001 interface ge-2/2/2.2001
  set routing-instances Local bridge-domains VL2001 interface ae1.2001
```

## "Сравнение +/-"
```bash
"Сравнение конфиг интерфейсов"  
  "Типичный интерпайз - конфиг интерфейсов"
    set interfaces xe-1/0/1 unit 2000 description "L2VPN"
    set interfaces xe-1/0/1 unit 2000 family bridge vlan-id-list 2000-2001
    set interfaces xe-1/0/1 unit 2000 family bridge interface-mode trunk
    set interfaces xe-1/0/1 unit 2000 family bridge policer input 100Mbit

  "Типичный пров - конфиг интерфейсов"
    set interfaces xe-1/0/1 unit 2000 description "L2VPN"
    set interfaces xe-1/0/1 unit 2000 vlan-id 2000
    set interfaces xe-1/0/1 unit 2000 encapsulation vlan-bridge
    set interfaces xe-1/0/1 unit 2000 family bridge policer input 100Mbit
```

```bash
"Сравнение конфиг в RI"
  "Типичный интерпайз - конфиг RI"
    set routing-instances Local bridge-domains VL2000 description "L2VPN"
    set routing-instances Local bridge-domains VL2000 vlan-id-list 2000-2001
    set routing-instances Local interface xe-1/0/1.2000

  "Типичный пров - конфиг RI"
    set routing-instances Local bridge-domains VL2000 description "L2VPN"
    set routing-instances Local bridge-domains VL2000 vlan-id 2000
    set routing-instances Local bridge-domains VL2000 interface xe-1/0/1.2000

    set routing-instances Local bridge-domains VL2001 description "L2VPN"
    set routing-instances Local bridge-domains VL2001 vlan-id 2001
    set routing-instances Local bridge-domains VL2001 interface xe-1/0/1.2001
```

## "+/-"


### "Enterprise style"

```bash
"Enterprise style"

Плюсы "Enterprise style"
Минусы "Enterprise style"
  1. Не работает storm-control
    set routing-instances Local apply-groups BD_storm-control
    set groups BD_storm-control routing-instances <*> bridge-domains <*> forwarding-options flood input storm_control
    set forwarding-options storm-control-profiles sc_broadcast_1m all bandwidth-level 1024
    set forwarding-options storm-control-profiles sc_broadcast_1m all no-unknown-unicast
    set forwarding-options storm-control-profiles sc_broadcast_1m all no-multicast
  2. Нельзя производить push/pop/swap 
```    

### "Provider style"

```bash
"Provider style"

!Плюсы "Provider style"
  1. Работает storm-control
    set routing-instances Local apply-groups BD_storm-control
    set groups BD_storm-control routing-instances <*> bridge-domains <*> forwarding-options flood input storm_control
    set forwarding-options storm-control-profiles sc_broadcast_1m all bandwidth-level 1024
    set forwarding-options storm-control-profiles sc_broadcast_1m all no-unknown-unicast
    set forwarding-options storm-control-profiles sc_broadcast_1m all no-multicast
  2. Можно производить push/pop/swap 
!Минусы "Provider style"
```

[Лит-ра](https://habr.com/ru/post/322560/)