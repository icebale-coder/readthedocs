title: Nexus 9000

# Cisco Nexus 9000

## FEX

https://www.cisco.com/c/en/us/td/docs/switches/datacenter/nexus9000/sw/92x/fex/configuration/guide/b-cisco-nexus-2000-nx-os-fabric-extender-configuration-guide-for-nexus-9000-switches-92x/m_n9k_configuring_the_cisco_nexus_2000_series_fabric_extender_9x.html

По данной [таблице совместимости](https://www.cisco.com/c/dam/en/us/td/docs/Website/datacenter/fexmatrix/fexmatrix.html) для начала нужно выяснить совместимость вашего FEX с родительским свитчом, не забывайте обращать внимание на Additional Notes.

Например, в нашем случае выяснилось, что N2K-C2248PQ-10GE заводится с N9K-C93180YC-EX только через Breakout (4x10G)

1. Включить поддержку FEX в NX-OS:

   ```bash
   install feature-set fex
   feature-set fex
   ```

2. Создать Port-Channel и привязать его к FEX:

   ```bash
   interface range eth1/45-48
     description << Po48 to FEX101 >>
     channel-group 48
     no shutdown
   
   interface port-channel48
     description << FEX101 >>
     switchport
     switchport mode fex-fabric
     fex associate 101
   ```
   

## Полезные команды

* `show sprom all` - показывает в чистом виде много информацию по железу (сенсоры, burned-in mac и т.п.)

  ```bash
  NEW-D1# show sprom all 
  DISPLAY active supervisor sprom contents:
  Common block:
   Block Signature : 0xABAB
   Block Version   : 3
   Block Length    : 160
   Block Checksum  : 0x1CCD
   EEPROM Size     : 65535
   Block Count     : 3
   FRU Major Type  : 0x6002
   FRU Minor Type  : 0x0
   OEM String      : Cisco Systems, Inc.
   Product Number  : N9K-C93180YC-EX
   Serial Number   : FDACADACAD
   Part Number     : 73-17776-05
   Part Revision   : A0
   Mfg Deviation   : 0000000000000000000
   H/W Version     : 3.0
   Mfg Bits        : 0
   Engineer Use    : 0
   snmpOID         : 9.12.3.1.3.103.17.0
   Power Consump   : -4100
   RMA Code        : 0-0-0-0
   CLEI Code       : INM1300ARB
   VID             : V02
  ...
  ```

  Также возможно указание параметров у команды, которые позволяют вывести информацию только по конкретному модулю/FEXу и т.п.:

  ```bash
  NEW-D1# sh sprom ?
    all          Show all sproms contents
    backplane    Show backplane clock module sprom contents
    fan          Show fan module sprom contents
    fex          Fex
    module       Show linecard module sprom contents
    powersupply  Show powersupply sprom contents
    stby-sup     Show standby supervisor sprom contents
    sup          Show supervisor sprom contents
  ```

* `show interface x#/# capabilities` - показывает поддерживаемый на инте функционал:

  ```bash
  NEW-D1# sh int eth1/1 capabilities 
  Ethernet1/1
    Model:                 N9K-C93180YC-EX
    Type (SFP capable):    10Gbase-(unknown)
    Speed:                 100,1000,10000,25000
    Duplex:                full
    Trunk encap. type:     802.1Q
    Channel:               yes
    Broadcast suppression: percentage(0-100)
    Flowcontrol:           rx-(off/on),tx-(off/on)
    Rate mode:             dedicated
    Port mode:             Routed,Switched
    QOS scheduling:        rx-(8q2t),tx-(7q)
    CoS rewrite:           yes
    ToS rewrite:           yes
    SPAN:                  yes
    UDLD:                  yes
    MDIX:                  no
    TDR capable:           no
    Link Debounce:         yes
    Link Debounce Time:    yes
    FEX Fabric:            yes
    dot1Q-tunnel mode:     yes
    Pvlan Trunk capable:   no
    Port Group Members:    1
    EEE (efficient-eth):   no
    PFC capable:           yes
    Buffer Boost capable:  no
    Breakout capable:      no
    MACSEC capable:        no
  ```

  