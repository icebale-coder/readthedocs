# replace and copy

Если есть задачка перетащить настройки с одного интерфейса на другой или просто их скопировать, то 
в Juniper-е на помощь приходят команды "replace" и "copy"

## replace

``` bash
"replace pattern PATTERN1 with PATTERN2"

Работает как замена одного "PATTERN1" на "PATTERN2" во всей конфигурации
при этом "PATTERN2" не должно быть в конфиге иначе возникнет ошибка вида:

"user@MX# replace pattern ge-10/3/5 with ge-10/0/6"
  error: target statement 'ge-10/0/6' already exists
  [edit interfaces]
    'ge-10/3/5'
      could not rename to 'ge-10/0/6'

"user@MX> show configuration | display set | match ge-10/0/6"
  set interfaces ge-10/0/6 description "MY DESCRIPTION HERE"

Если упоминания ge-10/0/6 в конфиге нет, то тогда:

В данном куске конфига можно заменить "ge-10/3/5" на "ge-10/0/6"

"user@MX> show configuration | display set | match ge-10/3/5"
  set interfaces ge-10/3/5 description "!JP ISP"
  set interfaces ge-10/3/5 flexible-vlan-tagging
  set interfaces ge-10/3/5 mtu 9216
  set interfaces ge-10/3/5 encapsulation flexible-ethernet-services
  set interfaces ge-10/3/5 unit 999 description "L2VPN 999"
  set interfaces ge-10/3/5 unit 999 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 999 vlan-id 999
  set interfaces ge-10/3/5 unit 111 description "L2VPN 111"
  set interfaces ge-10/3/5 unit 111 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 111 vlan-id 111
  set routing-instances EVPN9 bridge-domains VL0999 interface ge-10/3/5.999
  set routing-instances EVPN1 bridge-domains VL0111 interface ge-10/3/5.111

"user@MX> configure private"                                     

"user@MX# replace pattern ge-10/3/5 with ge-10/0/6"    

ИТОГО:
"user@MX# show | compare" 
  [edit interfaces]
  +   ge-10/0/6 {
  +       description "!JP ISP";
  +       flexible-vlan-tagging;
  +       mtu 9216;
  +       encapsulation flexible-ethernet-services;
  +       unit 999 {
  +           description "L2VPN 999";
  +           encapsulation vlan-bridge;
  +           vlan-id 999;
  +       }
  +       unit 111 {
  +           description "L2VPN 111";
  +           encapsulation vlan-bridge;
  +           vlan-id 111;
  +       }
  +   }
  -   ge-10/3/5 {
  -       description "!JP ISP";
  -       flexible-vlan-tagging;
  -       mtu 9216;
  -       encapsulation flexible-ethernet-services;
  -       unit 999 {
  -           description "L2VPN 999";
  -           encapsulation vlan-bridge;
  -           vlan-id 999;
  -       }
  -       unit 111 {
  -           description "L2VPN 111";
  -           encapsulation vlan-bridge;
  -           vlan-id 111;
  -       }
  -   }
  [edit routing-instances EVPN9 bridge-domains VL0999]
  +      interface ge-10/0/6.999;
        interface ae1.999 { ... }
  [edit routing-instances EVPN9 bridge-domains VL0999]
  -      interface ge-10/3/5.999;
  [edit routing-instances EVPN1 bridge-domains VL0111]
  +      interface ge-10/0/6.111;
  -      interface ge-10/3/5.111;
```

## copy interface

```bash 
"Работает как копирование настроек одного интерфейска на другой"

Скопируем настройки данного интерфейса на другой
"user@MX# run show configuration interfaces ge-10/3/5 | display set"
  set interfaces ge-10/3/5 description "!JP ISP"
  set interfaces ge-10/3/5 flexible-vlan-tagging
  set interfaces ge-10/3/5 mtu 9216
  set interfaces ge-10/3/5 encapsulation flexible-ethernet-services
  set interfaces ge-10/3/5 unit 999 description "L2VPN 999"
  set interfaces ge-10/3/5 unit 999 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 999 vlan-id 999
  set interfaces ge-10/3/5 unit 111 description "L2VPN 111"
  set interfaces ge-10/3/5 unit 111 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 111 vlan-id 111

"user@MX# copy interfaces ge-10/3/5 to ge-10/0/6"

ИТОГО:
"user@MX# show | compare"                            
  [edit interfaces]
  +   ge-10/0/6 {
  +       description "!JP ISP";
  +       flexible-vlan-tagging;
  +       mtu 9216;
  +       encapsulation flexible-ethernet-services;
  +       unit 999 {
  +           description "L2VPN 999";
  +           encapsulation vlan-bridge;
  +           vlan-id 999;
  +       }
  +       unit 111 {
  +           description "L2VPN 111";
  +           encapsulation vlan-bridge;
  +           vlan-id 111;
  +       }
  +   }
```