title: tcpdump MX80

#JunOS monitor traffic (tcpdump)

В данном примере в ae0.1111 ходит только ospf трафик

## Загрузка tcpdump в файл 
!!! warning "Не забываем про настойки секции firewall для lo0!!!"
	Без разрешения для lo0 ip адреса ftp доступа на него не будет!!!

## Запуск monitor traffic
<details><summary>monitor traffic interface ae0.1111 detail no-resolve</summary>
<p>

```bash
admin@MBR0> monitor traffic interface ae0.1111 detail no-resolve                          

Address resolution is OFF.
Listening on ae0.3951, capture size 1514 bytes

16:42:08.200209 Out IP (tos 0xc0, ttl   1, id 50865, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.51 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.51, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 128
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.50
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:15.100380  In IP (tos 0xc0, ttl   1, id 48881, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.50 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.50, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.51
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:16.780067 Out IP (tos 0xc0, ttl   1, id 51440, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.51 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.51, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 128
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.50
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:24.829476  In IP (tos 0xc0, ttl   1, id 49393, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.50 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.50, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.51
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:26.045885 Out IP (tos 0xc0, ttl   1, id 52030, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.51 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.51, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 128
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.50
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:33.878343  In IP (tos 0xc0, ttl   1, id 49893, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.50 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.50, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 1
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.51
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
16:42:35.972702 Out IP (tos 0xc0, ttl   1, id 52588, offset 0, flags [none], proto: OSPF (89), length: 80) 111.111.111.51 > 224.0.0.5: OSPFv2, Hello, length 60 [len 48]
        Router-ID 111.111.111.51, Backbone Area, Authentication Type: none (0)
        Options [External, LLS]
          Hello Timer 10s, Dead Timer 40s, Mask 255.255.255.254, Priority 128
          Designated Router 111.111.111.51, Backup Designated Router 111.111.111.50
          Neighbor List:
            111.111.111.50
          LLS: checksum: 0xfff6, length: 3
            Extended Options (1), length: 4
              Options: 0x00000001 [LSDB resync]
7 packets received by filter
0 packets dropped by kernel
```
</p>
</details>

## Загрузка tcpdump в файл 
!!! note "по дефолту файл дампа хранится в директории /var/tmp"

### Загрузка tcpdump в файл на Juniper
```bash
monitor traffic interface ae0.1111 detail write-file /var/tmp/ae0.1111.pcap
```

### Копированиепо ftp

####  Копирование из shell-a
Просмотр данного файла в шелле (для этого нужно зайти под рутом системы, в данном случае рут это 'admin') 

```bash
start shell 
% su
Password:
root@MBR% who
admin    ttyu0    Mar  2 09:42 
```

```bash
Данный файл можно скопировать например через ftp
root@MBR% ftp ftp://myacc:mypass@10.10.10.10
Connected to 10.10.10.10.
220-FileZilla Server 0.9.60 beta
220-written by Tim Kosse (tim.kosse@filezilla-project.org)
220 Please visit https://filezilla-project.org/
Remote system type is UNIX.
331 Password required for cisco
230 Logged on
200 Type set to I
250 CWD successful. "/" is current directory.
ftp> send /var/tmp/ae0.1111.pcap ae0.1111.pcap
local: /var/tmp/ae0.1111.pcap remote: ae0.1111.pcap
200 Port command successful
150 Opening data channel for file upload to server of "/ae0.1111.pcap"
100% |**********************************************************************************************************************************************************|   554       00:00 ETAA
226 Successfully transferred "/ae0.1111.pcap"
554 bytes sent in 0.05 seconds (10.41 KB/s)
ftp>        quit
```

#### Можно скопировать файл на ftp и в операционном режиме
admin@MBR0> copy file ae0.1111.pcap ftp://myacc,mypass@10.10.10.10/ae0.1111.pcap 
ftp://myacc,mypass@10.10.10.10/ae0.1111 100% of  136  B   97 kBps

### Данный файл можно открыть в wireshark

Документация Juniper по [monitor traffic](https://www.juniper.net/documentation/us/en/software/junos/bgp/topics/ref/command/monitor-traffic.html) 


Документация Juniper по [TCP dump command and matching patterns with examples](https://kb.juniper.net/InfoCenter/index?page=content&id=KB33717&cat=PTX5000&actp=LIST)