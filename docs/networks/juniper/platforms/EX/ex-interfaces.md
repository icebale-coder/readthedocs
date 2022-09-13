title: EX interfaces

# "Карпускулярно-волновой дуализм" в EX 

<details><summary>Возьмем для примера такую вот железку:</summary>
<p>

```bash
  !user@EX4500_Test> show version 
    fpc0:
    --------------------------------------------------------------------------
    Hostname: EX4500_Test
    Model: ex4500-40f
    Junos: 15.1R7-S6.3
    JUNOS EX  Software Suite [15.1R7-S6.3]
    JUNOS FIPS mode utilities [15.1R7-S6.3]
    JUNOS Online Documentation [15.1R7-S6.3]
    JUNOS EX 4500 Software Suite [15.1R7-S6.3]
    JUNOS Web Management Platform Package [15.1R7-S6.3]
```
</p>
</details>

```bash 
Изначально SFP модульные интерфейсы можно конфигурить в двух вариантах "ge" и "xe"
В зависимости от вставленных в них модулей заработают те или иные интерфейсы. 
```

<details><summary>Изначальная конфигурация:</summary>
<p>

```bash
user@EX4500_Test> show configuration | display set | match "0/0/1 " 
  set interfaces ge-0/0/1 description test_1g
  set interfaces xe-0/0/1 description test_10g

user@EX4500_Test> show configuration | display set | match "0/0/14" 
  set interfaces xe-0/0/14 description test_10g
```
</p>
</details>

## Поставил 10G SFP+ модули

```bash
!user@EX4500_Test> show interfaces descriptions | match "0/0/1 |0/0/14"     
  ge-0/0/1                   test_1g
  xe-0/0/1     up    up      test_10g
  xe-0/0/14    up    up      test_10g
```

<details><summary>show chassis hardware</summary>
<p>

```bash
!user@EX4500_Test> show chassis hardware 
  Hardware inventory:
  Item             Version  Part number  Serial number     Description
  Chassis                                GG0211059052      EX4500-40F
  Routing Engine 0 REV 10   750-035702   GG0211059052      EX4500-40F
  FPC 0            REV 10   750-035702   GG0211059052      EX4500-40F
    CPU                     BUILTIN      BUILTIN           FPC CPU
    PIC 0                   BUILTIN      BUILTIN           40x 1/10GE
      Xcvr 1       REV 01   740-021309   PT22010306946L    SFP+-10G-LR
      Xcvr 14      REV 01   740-021309   PT22010306787L    SFP+-10G-LR
```
</p>
</details>

<details><summary>show interfaces diagnostics optics xe-0/0/1</summary>
<p>

```bash

!user@EX4500_Test> show interfaces diagnostics optics xe-0/0/1         
  Physical interface: xe-0/0/1
      Laser bias current                        :  33.726 mA
      Laser output power                        :  1.1420 mW / 0.58 dBm
      Module temperature                        :  26 degrees C / 78 degrees F
      Module voltage                            :  3.2000 V
      Receiver signal average optical power     :  0.8568 mW / -0.67 dBm
      Laser bias current high alarm             :  Off
      Laser bias current low alarm              :  Off
      Laser bias current high warning           :  Off
      Laser bias current low warning            :  Off
      Laser output power high alarm             :  Off
      Laser output power low alarm              :  Off
      Laser output power high warning           :  Off
      Laser output power low warning            :  Off
      Module temperature high alarm             :  Off
      Module temperature low alarm              :  Off
      Module temperature high warning           :  Off
      Module temperature low warning            :  Off
      Module voltage high alarm                 :  Off
      Module voltage low alarm                  :  Off
      Module voltage high warning               :  Off
      Module voltage low warning                :  Off
      Laser rx power high alarm                 :  Off
      Laser rx power low alarm                  :  Off
      Laser rx power high warning               :  Off
      Laser rx power low warning                :  Off
      Laser bias current high alarm threshold   :  100.000 mA
      Laser bias current low alarm threshold    :  10.000 mA
      Laser bias current high warning threshold :  90.000 mA
      Laser bias current low warning threshold  :  15.000 mA
      Laser output power high alarm threshold   :  3.9810 mW / 6.00 dBm
      Laser output power low alarm threshold    :  0.0950 mW / -10.22 dBm
      Laser output power high warning threshold :  3.1620 mW / 5.00 dBm
      Laser output power low warning threshold  :  0.1510 mW / -8.21 dBm
      Module temperature high alarm threshold   :  85 degrees C / 185 degrees F
      Module temperature low alarm threshold    :  -10 degrees C / 14 degrees F
      Module temperature high warning threshold :  75 degrees C / 167 degrees F
      Module temperature low warning threshold  :  -5 degrees C / 23 degrees F
      Module voltage high alarm threshold       :  3.600 V
      Module voltage low alarm threshold        :  3.000 V
      Module voltage high warning threshold     :  3.500 V
      Module voltage low warning threshold      :  3.100 V
      Laser rx power high alarm threshold       :  3.1623 mW / 5.00 dBm
      Laser rx power low alarm threshold        :  0.0126 mW / -19.00 dBm
      Laser rx power high warning threshold     :  2.5119 mW / 4.00 dBm
      Laser rx power low warning threshold      :  0.0251 mW / -16.00 dBm
```
</p>
</details>


## Поставил 1G SFP медные модули  

<details><summary>show chassis hardware</summary>
<p>

```bash
!user@EX4500_Test> show chassis hardware                                    
  Hardware inventory:
  Item             Version  Part number  Serial number     Description
  Chassis                                GG0211059052      EX4500-40F
  Routing Engine 0 REV 10   750-035702   GG0211059052      EX4500-40F
  FPC 0            REV 10   750-035702   GG0211059052      EX4500-40F
    CPU                     BUILTIN      BUILTIN           FPC CPU
    PIC 0                   BUILTIN      BUILTIN           40x 1/10GE
      Xcvr 1                NON-JNPR     S1705170282       SFP-T
      Xcvr 14      REV 02   740-013111   TLASA80095        SFP-T
```
</p>
</details>

```bash
!user@EX4500_Test> show configuration | display set | match "0/0/1 "   
  set interfaces ge-0/0/1 description test_1g
  set interfaces xe-0/0/1 description test_10g
```

```bash
!user@EX4500_Test> show configuration | display set | match "0/0/14"   
  set interfaces xe-0/0/14 description test_10g

!user@EX4500_Test> show interfaces descriptions | match "0/0/1 |0/0/14"     
  ge-0/0/1        up    up   test_1g
  xe-0/0/1                   test_10g
  xe-0/0/14                  test_10g
```

<details><summary>show interfaces ge-0/0/1</summary>
<p>

```bash
!user@EX4500_Test> show interfaces ge-0/0/1                         
  Physical interface: ge-0/0/1, Enabled, Physical link is Up
    Interface index: 143, SNMP ifIndex: 519
    Link-level type: Ethernet, MTU: 1514, LAN-PHY mode, Speed: Auto, Duplex: Auto,
    BPDU Error: None, MAC-REWRITE Error: None, Loopback: Disabled,
    Source filtering: Disabled, Flow control: Enabled, Auto-negotiation: Enabled,
    Remote fault: Online, Media type: Copper
    Device flags   : Present Running
    Interface flags: SNMP-Traps Internal: 0x0
    Link flags     : None
    CoS queues     : 8 supported, 8 maximum usable queues
    Current address: 84:18:88:ae:f4:91, Hardware address: 84:18:88:ae:f4:91
    Last flapped   : 2022-08-18 16:08:43 GMT-3 (00:19:31 ago)
    Input rate     : 0 bps (0 pps)
    Output rate    : 0 bps (0 pps)
    Active alarms  : None
    Active defects : None
    Interface transmit statistics: Disabled
```
</p>
</details>

<details><summary>show interfaces ge-0/0/1</summary>
<p>

```bash
!user@EX4500_Test> show interfaces ge-0/0/14                         
  Physical interface: ge-0/0/14, Enabled, Physical link is Up
    Interface index: 143, SNMP ifIndex: 519
    Link-level type: Ethernet, MTU: 1514, LAN-PHY mode, Speed: Auto, Duplex: Auto,
    BPDU Error: None, MAC-REWRITE Error: None, Loopback: Disabled,
    Source filtering: Disabled, Flow control: Enabled, Auto-negotiation: Enabled,
    Remote fault: Online, Media type: Copper
    Device flags   : Present Running
    Interface flags: SNMP-Traps Internal: 0x0
    Link flags     : None
    CoS queues     : 8 supported, 8 maximum usable queues
    Current address: 84:18:88:ae:f4:92, Hardware address: 84:18:88:ae:f4:92
    Last flapped   : 2022-08-18 16:08:43 GMT-3 (00:19:31 ago)
    Input rate     : 0 bps (0 pps)
    Output rate    : 0 bps (0 pps)
    Active alarms  : None
    Active defects : None
    Interface transmit statistics: Disabled

!user@EX4500_Test> show interfaces xe-0/0/1     
  error: device xe-0/0/1 not found

!user@EX4500_Test> show interfaces xe-0/0/14   
  error: device xe-0/0/14 not found
```
</p>
</details>

## Выводы

```bash
По сути при установкe модуля происходит его детектирование,
что видно по выводу команды "show chassis hardware".

А в зависимости от применяемой конфигурации модуль 
уже включается в логику работы оборудования.
Нету интерфейса в конфиге, нельзы будет использовать его в логике работы.
```

## Бонус

```bash
"И даже более того, можно делать совершенно разные настройки для разных типов интерфейсов!"
"Тогда по сути при смене модулей может измениться и логика работы! )"
```

```bash
  !user@EX4500_Test> show configuration | match "0/0/1 " | display set    
    set interfaces ge-0/0/1 description test_1g
    set interfaces ge-0/0/1 unit 0 family ethernet-switching port-mode trunk
    set interfaces ge-0/0/1 unit 0 family ethernet-switching vlan members VL0300
    set interfaces xe-0/0/1 description test_10g
    set interfaces xe-0/0/1 unit 0 family ethernet-switching vlan members VL0100
```