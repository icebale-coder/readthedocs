title: snmpv3

# Настройка SNMPv3

## Описание 
По snmp 

### без шифрования

```bash
"можно указать какие oid-ы доступны для просмотра"
snmp-server view DeviceView iso.3.6.1.2.1.2.2.1.2 included
snmp-server group snmp_group v3 noauth read DeviceView 
snmp-server user USER_NAME1 snmp_group v3 auth md5 MY_PASSWORD
```

```bash
"проверка работоспособности:"
"snmpwalk -v 3 -u имя_пользователя -A ПАРОЛЬ -l authNoPriv хост"
snmpwalk -v 3 -u USER_NAME1 -A MY_PASSWORD -l authNoPriv 1.1.1.1
```

### с шифроваением

```bash
snmp-server group snmp_group v3 auth read DeviceView
snmp-server view DeviceView ifTable included
snmp-server user USER_NAME1 snmp_group v3 auth md5 MY_PASSWORD_AUTH priv des MY_PASSWORD_CRYPT

"или во втором случае пример с другими параметрами авторизации и протоколом шифрования"
snmp-server user USER_NAME1 snmp_group v3 auth sha MY_PASSWORD_AUTH priv aes 128 MY_PASSWORD_CRYPT
```

```bash
"проверка работоспособности:"
snmpwalk -v 3 -a MD5 -A MY_PASSWORD_AUTH -l authPriv -u USER_NAME1 -x DES -X MY_PASSWORD_CRYPT

"или во втором случае:"
snmpwalk -v 3 -a SHA -A MY_PASSWORD_AUTH -l authPriv -u имя_пользователя -x AES 128 -X MY_PASSWORD_CRYPT
```

### Diagnostic

<details><summary>Cisco</summary>
<p>

```bash
  "sh snmp group"
    groupname: ILMI                             security model:v1 
    contextname: <no context specified>         storage-type: permanent
    readview : *ilmi                            writeview: *ilmi                           
    notifyview: <no notifyview specified>       
    row status: active

    groupname: ILMI                             security model:v2c 
    contextname: <no context specified>         storage-type: permanent
    readview : *ilmi                            writeview: *ilmi                           
    notifyview: <no notifyview specified>       
    row status: active

    groupname: snmp_group                       security model:v3 noauth 
    contextname: <no context specified>         storage-type: nonvolatile
    readview : DeviceView                       writeview: <no writeview specified>        
    notifyview: <no notifyview specified>       
    row status: active

  "sh snmp user"
    User name: USER_NAME1
    Engine ID: 80000009030000FEC894E280
    storage-type: nonvolatile	 active
    Authentication Protocol: MD5
    Privacy Protocol: DES
    Group-name: snmp_group

  "sh snmp view"
    ---cat---
    v1default ciscoUserConnectionTapMIB - excluded permanent active
    v1default ciscoVoIpTapMIB - excluded permanent active
    DeviceView ifTable - included nonvolatile active

```

</p>
</details>


<details><summary>snmpwalk</summary>
<p>

```bash
 "snmpwalk -v 3 -a MD5 -A MY_PASSWORD_AUTH -l authPriv -u USER_NAME1 -x DES -X MY_PASSWORD_CRYPT 1.1.1.1"
 
 "iso.3.6.1.2.1.2.2.1.1.6 = INTEGER: 6"
    iso.3.2.1.2.2.1.1.2 = INTEGER: 
    iso.3.6.1.2.1.2.2.1.1.4 = INTEGER: 4
    iso.3.6.1.2.1.2.2.1.1.5 =2.  DES  
    iso.3.6.1.2.1.2.2.1.1.7 = INTEGER: 7
    iso.3.6.1.2.1.2.2.1.1.9 = INTEGER: 9
    iso.3.6.1.2.1.2.2.1.1.10 = INTEGER: 10
    iso.3.6.1.2.1.2.2.1.1.11 = INTEGER: 11
    iso.3.6.1.2.1.2.2.1.2.1 = STRING: "GigabitEthernet0/0/0"
    iso.3.6.1.2.1.2.2.1.2.2 = STRING: "GigabitEthernet0/0/1"
    iso.3.6.1.2.1.2.2.1.2.3 = STRING: "GigabitEthernet0/0/2"
    iso.3.6.1.2.1.2.2.1.2.4 = STRING: "GigabitEthernet0/0/3"
    iso.3.6.1.2.1.2.2.1.2.5 = STRING: "GigabitEthernet0"
    iso.3.6.1.2.1.2.2.1.2.6 = STRING: "VoIP-Null0"
    iso.3.6.1.2.1.2.2.1.2.7 = STRING: "Null0"
    iso.3.6.1.2.1.2.2.1.2.8 = STRING: "Loopback0"
    iso.3.6.1.2.1.2.2.1.2.9 = STRING: "GigabitEthernet0/0/3.19"
    iso.3.6.1.2.1.2.2.1.2.10 = STRING: "GigabitEthernet0/0/0.87"
    iso.3.6.1.2.1.2.2.1.2.11 = STRING: "GigabitEthernet0/0/1.80"
    iso.3.6.1.2.1.2.2.1.3.1 = INTEGER: 6
    iso.3.6.1.2.1.2.2.1.3.2 = INTEGER: 6
    iso.3.6.1.2.1.2.2.1.3.3 = INTEGER: 6
    iso.3.6.1.2.1.2.2.1.3.4 = INTEGER: 6
    iso.3.6.1.2.1.2.2.1.3.5 = INTEGER: 6
    iso.3.6.1.2.1.2.2.1.3.6 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.3.7 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.3.8 = INTEGER: 24
    iso.3.6.1.2.1.2.2.1.3.9 = INTEGER: 135
    iso.3.6.1.2.1.2.2.1.3.10 = INTEGER: 135
    iso.3.6.1.2.1.2.2.1.3.11 = INTEGER: 135
    iso.3.6.1.2.1.2.2.1.4.1 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.2 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.3 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.4 = INTEGER: 9216
    iso.3.6.1.2.1.2.2.1.4.5 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.6 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.7 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.8 = INTEGER: 1514
    iso.3.6.1.2.1.2.2.1.4.9 = INTEGER: 9216
    iso.3.6.1.2.1.2.2.1.4.10 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.4.11 = INTEGER: 1500
    iso.3.6.1.2.1.2.2.1.5.1 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.2 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.3 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.4 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.5 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.6 = Gauge32: 4294967295
    iso.3.6.1.2.1.2.2.1.5.7 = Gauge32: 4294967295
    iso.3.6.1.2.1.2.2.1.5.8 = Gauge32: 4294967295
    iso.3.6.1.2.1.2.2.1.5.9 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.10 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.5.11 = Gauge32: 1000000000
    iso.3.6.1.2.1.2.2.1.6.1 = Hex-STRING: 00 FE C8 94 E2 80 
    iso.3.6.1.2.1.2.2.1.6.2 = Hex-STRING: 00 FE C8 94 E2 81 
    iso.3.6.1.2.1.2.2.1.6.3 = Hex-STRING: 00 FE C8 94 E2 82 
    iso.3.6.1.2.1.2.2.1.6.4 = Hex-STRING: 00 FE C8 94 E2 83 
    iso.3.6.1.2.1.2.2.1.6.5 = Hex-STRING: 00 FE C8 94 E2 C0 
    iso.3.6.1.2.1.2.2.1.6.6 = ""
    iso.3.6.1.2.1.2.2.1.6.7 = ""
    iso.3.6.1.2.1.2.2.1.6.8 = ""
    iso.3.6.1.2.1.2.2.1.6.9 = Hex-STRING: 00 FE C8 94 E2 83 
    iso.3.6.1.2.1.2.2.1.6.10 = Hex-STRING: 00 FE C8 94 E2 80 
    iso.3.6.1.2.1.2.2.1.6.11 = Hex-STRING: 00 FE C8 94 E2 81 
    iso.3.6.1.2.1.2.2.1.7.1 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.2 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.3 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.4 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.5 = INTEGER: 2
    iso.3.6.1.2.1.2.2.1.7.6 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.7 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.8 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.9 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.10 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.7.11 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.1 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.2 = INTEGER: 2
    iso.3.6.1.2.1.2.2.1.8.3 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.4 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.5 = INTEGER: 2
    iso.3.6.1.2.1.2.2.1.8.6 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.7 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.8 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.9 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.10 = INTEGER: 1
    iso.3.6.1.2.1.2.2.1.8.11 = INTEGER: 7
    iso.3.6.1.2.1.2.2.1.9.1 = Timeticks: (164404) 0:27:24.04
    iso.3.6.1.2.1.2.2.1.9.2 = Timeticks: (260123197) 30 days, 2:33:51.97
    iso.3.6.1.2.1.2.2.1.9.3 = Timeticks: (1995234) 5:32:32.34
    iso.3.6.1.2.1.2.2.1.9.4 = Timeticks: (167034) 0:27:50.34
    iso.3.6.1.2.1.2.2.1.9.5 = Timeticks: (1647) 0:00:16.47
    iso.3.6.1.2.1.2.2.1.9.6 = Timeticks: (1440) 0:00:14.40
    iso.3.6.1.2.1.2.2.1.9.7 = Timeticks: (0) 0:00:00.00
    iso.3.6.1.2.1.2.2.1.9.8 = Timeticks: (1611) 0:00:16.11
    iso.3.6.1.2.1.2.2.1.9.9 = Timeticks: (167234) 0:27:52.34
    iso.3.6.1.2.1.2.2.1.9.10 = Timeticks: (181491334) 21 days, 0:08:33.34
    iso.3.6.1.2.1.2.2.1.9.11 = Timeticks: (260123197) 30 days, 2:33:51.97
    iso.3.6.1.2.1.2.2.1.10.1 = Counter32: 273198184
    iso.3.6.1.2.1.2.2.1.10.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.10.3 = Counter32: 554831860
    iso.3.6.1.2.1.2.2.1.10.4 = Counter32: 1854147679
    iso.3.6.1.2.1.2.2.1.10.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.10.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.10.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.10.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.10.9 = Counter32: 1854746451
    iso.3.6.1.2.1.2.2.1.10.10 = Counter32: 200337202
    iso.3.6.1.2.1.2.2.1.10.11 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.1 = Counter32: 294708
    iso.3.6.1.2.1.2.2.1.11.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.3 = Counter32: 196158
    iso.3.6.1.2.1.2.2.1.11.4 = Counter32: 15279447
    iso.3.6.1.2.1.2.2.1.11.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.11.9 = Counter32: 15279474
    iso.3.6.1.2.1.2.2.1.11.10 = Counter32: 1009
    iso.3.6.1.2.1.2.2.1.11.11 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.1 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.3 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.4 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.13.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.1 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.3 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.4 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.14.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.1 = Counter32: 54421
    iso.3.6.1.2.1.2.2.1.15.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.3 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.4 = Counter32: 1
    iso.3.6.1.2.1.2.2.1.15.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.15.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.1 = Counter32: 539600933
    iso.3.6.1.2.1.2.2.1.16.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.3 = Counter32: 604628976
    iso.3.6.1.2.1.2.2.1.16.4 = Counter32: 1710689950
    iso.3.6.1.2.1.2.2.1.16.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.16.9 = Counter32: 1710771878
    iso.3.6.1.2.1.2.2.1.16.10 = Counter32: 292591713
    iso.3.6.1.2.1.2.2.1.16.11 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.1 = Counter32: 2227421
    iso.3.6.1.2.1.2.2.1.17.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.3 = Counter32: 1300957
    iso.3.6.1.2.1.2.2.1.17.4 = Counter32: 10398625
    iso.3.6.1.2.1.2.2.1.17.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.17.9 = Counter32: 10408525
    iso.3.6.1.2.1.2.2.1.17.10 = Counter32: 2227874
    iso.3.6.1.2.1.2.2.1.17.11 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.1 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.3 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.4 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.19.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.1 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.2 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.3 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.4 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.5 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.6 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.7 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.8 = Counter32: 0
    iso.3.6.1.2.1.2.2.1.20.8 = No more variables left in this MIB View (It is past the end of the MIB tree)
```

</p>
</details>


