title: c4900M troubleshooting

Troubleshooting
# Описание ситуации:

	- Имеется WS-C4900M
	- Данное оборудование в полке по CPU
	- Имеется резкое возрастание unicast трафика на двух портах



## Смотрим нагрузку на CPU
<details><summary>C4900M#show processes cpu sorted 5sec | exclude 0.0</summary>
<p>

```bash
	CPU utilization for five seconds: 99%/0%; one minute: 99%; five minutes: 98%
	 PID Runtime(ms)     Invoked      uSecs   5Sec   1Min   5Min TTY Process 
	  79   574642564  1549442216        370 67.03% 64.60% 62.71%   0 Cat4k Mgmt LoPri 
	  78  2962062314  2501157908          0 13.51% 15.17% 15.57%   0 Cat4k Mgmt HiPri 
	  18  3903432698  1409468093       2769  9.83%  9.18%  9.64%   0 ARP Input        
	 315   399749140   917894620        435  2.55%  2.27%  2.37%   0 DHCPD Receive    
	 144  1288195768  3373610585          0  2.55%  2.33%  2.38%   0 IP Input         
	 186   581821135   584284704        995  1.11%  1.09%  1.11%   0 ADJ background   
	 202    62348119   783281362         79  0.55%  0.52%  0.51%   0 IGMPSN           
	 201     1375019    46594990         29  0.15%  0.16%  0.15%   0 IGMPSN MRD     
```

</p>
</details>

## Смотрим статистику по пакетам на CPU

<details><summary>C4900M#show platform cpu packet statistics</summary>
<p>

```bash
	RkGenericPacketMan:
	Packet allocation failures: 0
	Packet Buffer(SW Common) allocation failures: 0
	Packet Buffer(SW ESMP) allocation failures: 0
	Packet Buffer(SW EOBC) allocation failures: 0
	Packet Buffer(SW SupToSup) allocation failures: 0

	Packets Dropped In Processing Overall

	Total                5 sec avg 1 min avg 5 min avg 1 hour avg
	-------------------- --------- --------- --------- ----------
	           863897376      3088      2715      2007        848

	Packets Dropped In Processing by CPU event

	Event             Total                5 sec avg 1 min avg 5 min avg 1 hour avg
	----------------- -------------------- --------- --------- --------- ----------
	Sa Miss                       57438587      2865      2475      1827        746
	ESMP                                74         0         0         0          0
	L2 Control                          71         0         0         0          0
	L2 Router                          302         0         0         0          0
	L3 Receive                           1         0         0         0          0
	Input ACl Copy               222318762       134       147       110         44
	Output Acl Copy              582272296        87        76        58         48
	Sw Packet for Bridge              1867283         0         0         0          0

	Packets Dropped In Processing by Priority

	Priority          Total                5 sec avg 1 min avg 5 min avg 1 hour avg
	----------------- -------------------- --------- --------- --------- ----------
	Normal                       764765982       106        95        77         61
	Medium                        59635387      2865      2475      1827        746
	High                          39493497       115       128        90         33
	Crucial                           2436         0         0         0          0
	Super Crucial                       74         0         0         0          0

	Packets Dropped In Processing by Reason

	Reason             Total                5 sec avg 1 min avg 5 min avg 1 hour avg
	------------------ -------------------- --------- --------- --------- ----------
	STPDrop                               2         0         0         0          0
	AclActionDrop                   1693096         6         0         0          0
	NoDstPorts                    582446483        81        68        54         48
	Tx Mode Drop                  279757494      3000      2629      1944        793
	Extra packet due to RxProc hwAssisted Bridging                  301         0         0         0          0
	          
	Total packet queues 64

	Packets Received by Packet Queue

	Queue                  Total           5 sec avg 1 min avg 5 min avg 1 hour avg
	---------------------- --------------- --------- --------- --------- ----------
	Esmp                                79         0         0         0          0
	Input ACL fwd(snooping)       640218199        19         8         6          3
	Host Learning                 57432555      2885      2477      1828        746
	L2 Control                    60042963         0         0         0          0
	Input ACL log, unreach       247567486       135       145       110         45
	L2 bridge to CPU, 0             457945         0         0         0          0
	Ip Option                        24250         0         0         0          0
	Ttl Expired                  203026586         6         0         2          3
	Non Arpa                            77         0         0         0          0
	InputIf Fail                   8759182         0         0         0          0
	Output ACL log, unreach       582162895        90        75        58         48
	Mtu Fail                        597159         0         0         0          0
	Bfd                          333034406         0         0         0          0
	L2 router to CPU, 7        24411058697      1858      1184       945        596
	L3 Glean, 7                 2819957357        66        53        43         41
	L3 Fwd, 7                     31291493         3         0         0          0
	L3 Receive, 7               8297309377       260       234       186        177

	Packets Dropped by Packet Queue

	Queue                  Total           5 sec avg 1 min avg 5 min avg 1 hour avg
	---------------------- --------------- --------- --------- --------- ----------
	Host Learning                 20714469     13792     11417      7834       2766
	Input ACL log, unreach              51         0         0         0          0
	Ttl Expired                   18916753         0         0         0          0
	Output ACL log, unreach          289475         0         0         0          0
	L2 router to CPU, 7             348659         0         0         0          0
	L3 Glean, 7                    1390560         0         0         0          0
	L3 Fwd, 7                       122644         0         0         0          0
	L3 Receive, 7                  3588027         0         0         0          0
```
</p>
</details>

## Анализ статистики
```bash
Резко возрос SA Miss
SA Miss = This CPU event, is caused when a new MAC is seen on the port, 
and it is thus, a copy of such packet is forwarded to CPU for MAC learning.

SA Miss = Это событие ЦП вызывается, когда на порту виден новый MAC-адрес, 
и, таким образом, копия такого пакета пересылается в ЦП для изучения MAC.
```

[Описание SA miss](https://community.cisco.com/t5/switching/ws-c4510r-bb1-bb2-high-cpu-issue-through-the-memory-leak/td-p/3957760) 


## Включаем дебаг - в циклический буфер записываются пакеты

<details><summary>C4900M#debug platform packet all buffer</summary>
<p>

```bash
	platform packet debugging is on 
	C4900M#show platform cpu packet buffered
	Total Received Packets Buffered: 1024
	-------------------------------------
	Index 0:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: Output Acl Copy, Flags: 0x40, Size: 102
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:6999 fragOffset:0 ttl:61 proto:icmp
	    src: 10.253.255.2 dst: 172.23.67.7 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x41 0xDE 0xB5 0xF8 0x0  0x29 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 1:
	Mar 30 14:34:15.244 KRAT - RxVlan: 11, RxPort: Te1/6
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:12:17:E3:3A:0C Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0x12 
	10: 0x17 0xE3 0x3A 0xC  0xC0 0xA8 0x1  0x9  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xC0 0xA8 0x1  0x1B 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0xA8 0x8  0x92 0x22 
	          
	Index 2:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: High, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 68:72:51:82:19:50 Dst 01:00:5E:00:00:01 Type/Len 0x0800
	Ip: ver:IpVersion4 len:24 tos:192 totLen:32 id:0 fragOffset:0 ttl:1 proto:igmp
	    src: 192.168.160.58 dst: 224.0.0.1 hasIpOptions firstFragment lastFragment
	Remaining data: 
	 0: 0x11 0x64 0xEE 0x9B 0x0  0x0  0x0  0x0  0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x94 0x87 0xE1 0xF9 

	Index 3:
	Mar 30 14:34:15.244 KRAT - RxVlan: 2166, RxPort: Gi2/5
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src EC:AD:E0:1E:B9:97 Dst D0:D0:FD:93:18:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0xEC 0xAD 
	10: 0xE0 0x1E 0xB9 0x97 0xAC 0x14 0xE0 0x1A 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xAC 0x14 0xE0 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x29 0x88 0x56 0x61 

	Index 4:  
	Mar 30 14:34:15.244 KRAT - RxVlan: 2102, RxPort: Te1/6
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 98:DA:C4:F4:76:D8 Dst D0:D0:FD:93:18:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x98 0xDA 
	10: 0xC4 0xF4 0x76 0xD8 0xAC 0x14 0xDF 0x42 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xAC 0x14 0xDF 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x59 0xC6 0xDF 0xE9 

	Index 5:
	Mar 30 14:34:15.244 KRAT - RxVlan: 3869, RxPort: Te1/6
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:27:07:00:09:B8 Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0x27 
	10: 0x7  0x0  0x9  0xB8 0x5E 0x49 0xD7 0xE5 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x5E 0x49 0xD7 0xE6 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x8A 0x4A 0x22 0x9C 

	Index 6:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:AD:24:31:EB:6B Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0xAD 
	10: 0x24 0x31 0xEB 0x6B 0xC0 0xA8 0xA0 0xBB 0xFF 0xFF 
	20: 0xFF 0xFF 0xFF 0xFF 0xC0 0xA8 0xA0 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x7A 0xFF 0x94 0x9D 

	Index 7:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:AD:24:31:EB:6B Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0xAD 
	10: 0x24 0x31 0xEB 0x6B 0xC0 0xA8 0xA0 0xBB 0xFF 0xFF 
	20: 0xFF 0xFF 0xFF 0xFF 0xC0 0xA8 0xA0 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x7A 0xFF 0x94 0x9D 

	Index 8:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:AD:24:31:EB:6B Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0xAD 
	10: 0x24 0x31 0xEB 0x6B 0xC0 0xA8 0xA0 0xBB 0xFF 0xFF 
	20: 0xFF 0xFF 0xFF 0xFF 0xC0 0xA8 0xA0 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x7A 0xFF 0x94 0x9D 

	Index 9:
	Mar 30 14:34:15.244 KRAT - RxVlan: 2137, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 70
	Eth: Src C8:6C:87:42:43:3E Dst 33:33:00:00:00:02 Type/Len 0x86DD
	Remaining data: 
	 0: 0x60 0x0  0x0  0x0  0x0  0x8  0x3A 0xFF 0xFE 0x80 
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0xCA 0x6C 0x87 0xFF 
	20: 0xFE 0x42 0x43 0x3E 0xFF 0x2  0x0  0x0  0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x2  
	40: 0x85 0x0  0xE9 0x49 0x0  0x0  0x0  0x0  0x0  0x0  

	Index 10:
	Mar 30 14:34:15.244 KRAT - RxVlan: 84, RxPort: Te1/8
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src 44:03:A7:E8:FF:FF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:7197 fragOffset:0 ttl:245 proto:tcp
	    src: 194.147.140.65 dst: 109.226.196.31 firstFragment lastFragment
	Remaining data: 
	 0: 0xE8 0xE1 0x28 0x51 0xF6 0x3A 0xF0 0x53 0x0  0x0  
	10: 0x0  0x0  0x50 0x2  0x4  0x0  0x33 0x4A 0x0  0x0  
	20: 0x0  0x0  0xBE 0xDC 0xE7 0x5A 0x12 0x92 0xD5 0x25 

	Index 11:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:54321 fragOffset:0 ttl:237 proto:tcp
	    src: 192.35.168.99 dst: 94.73.232.10 firstFragment lastFragment
	Remaining data: 
	 0: 0xB1 0x62 0x1  0xBB 0x34 0x1A 0x56 0x5  0x0  0x0  
	10: 0x0  0x0  0x50 0x2  0xFF 0xFF 0xC3 0xCA 0x0  0x0  
	20: 0x0  0x0  0xAA 0xA9 0xD2 0x73 0xCD 0xD0 0xE7 0x3D 

	Index 12:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:43578 fragOffset:0 ttl:245 proto:tcp
	    src: 45.146.165.154 dst: 109.226.211.166 firstFragment lastFragment
	Remaining data: 
	 0: 0xD9 0x6E 0x4B 0x8A 0xDB 0xCC 0x32 0x16 0x0  0x0  
	10: 0x0  0x0  0x50 0x4  0x4  0xB0 0x63 0x9F 0x0  0x0  
	20: 0x0  0x0  0x44 0x16 0x38 0xFE 0xAD 0x88 0x5F 0x2E 

	Index 13:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:12528 fragOffset:0 ttl:245 proto:tcp
	    src: 45.146.166.200 dst: 109.226.253.10 firstFragment lastFragment
	Remaining data: 
	 0: 0xD9 0x92 0x58 0xBE 0x9C 0x20 0x95 0x44 0x0  0x0  
	10: 0x0  0x0  0x50 0x4  0x4  0xB0 0x8  0x33 0x0  0x0  
	20: 0x0  0x0  0xF1 0x44 0xA  0x23 0xAD 0x88 0x5F 0x2E 

	Index 14:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:45175 fragOffset:0 ttl:246 proto:tcp
	    src: 45.146.165.191 dst: 80.65.25.188 firstFragment lastFragment
	Remaining data: 
	 0: 0xD9 0x5F 0x49 0x32 0x78 0x80 0xE0 0xF9 0x0  0x0  
	10: 0x0  0x0  0x50 0x2  0x4  0x0  0xF2 0x87 0x0  0x0  
	20: 0x0  0x0  0xE8 0xD  0x3D 0xE6 0x3A 0xD0 0xF  0x16 

	Index 15:
	Mar 30 14:34:15.244 KRAT - RxVlan: 59, RxPort: Te1/7
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Receive, Flags: 0x440, Size: 64
	Eth: Src B0:AA:77:A2:10:BF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:40 id:18072 fragOffset:0 ttl:245 proto:tcp
	    src: 185.236.11.202 dst: 94.73.199.107 firstFragment lastFragment
	Remaining data: 
	 0: 0xB0 0xA6 0x19 0x8D 0xFE 0x2A 0x98 0x7E 0x0  0x0  
	10: 0x0  0x0  0x50 0x2  0x4  0x0  0x5F 0x9A 0x0  0x0  
	20: 0x0  0x0  0xD2 0xB4 0x0  0x64 0x19 0xCA 0xA5 0xC0 

	Index 16:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst F8:F0:82:4B:20:F7 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:64270 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.89 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x44 0x2E 0xB3 0x5F 0x0  0x72 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 17:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst F8:F0:82:4B:20:F7 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:64270 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.89 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x44 0x2E 0xB3 0x5F 0x0  0x72 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 18:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst F8:F0:82:4B:20:F7 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:64270 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.89 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x44 0x2E 0xB3 0x5F 0x0  0x72 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 19:
	Mar 30 14:34:15.244 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst F8:F0:82:4B:20:F7 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:64270 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.89 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x44 0x2E 0xB3 0x5F 0x0  0x72 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 20:
	Mar 30 14:34:22.388 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst D8:FE:E3:84:84:C0 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:11688 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.212.38 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3A 0x9E 0xBD 0x48 0x0  0x19 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 21:
	Mar 30 14:34:22.416 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:C7:17 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:23151 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.203 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3C 0x67 0xBB 0x7D 0x0  0x1B 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 22:
	Mar 30 14:34:22.416 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:62:B0:90 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:3375 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.212 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3D 0xFB 0xB9 0x89 0x0  0x7B 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 23:
	Mar 30 14:34:22.840 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 64
	Eth: Src D0:D0:FD:93:18:FF Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0xD0 0xD0 
	10: 0xFD 0x93 0x18 0xFF 0xC0 0xA8 0xF4 0xFE 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xC0 0xA8 0xF4 0xD2 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0xF9 0x65 0x4B 0x85 

	Index 24:
	Mar 30 14:34:22.840 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src D0:D0:FD:93:18:FF Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0xD0 0xD0 
	10: 0xFD 0x93 0x18 0xFF 0xC0 0xA8 0xF4 0xFE 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xC0 0xA8 0xF4 0xD2 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0xF9 0x65 0x4B 0x85 
	          
	Index 25:
	Mar 30 14:34:22.888 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:42:F0 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:27961 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.77 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3A 0xEE 0xBC 0xFE 0x0  0x13 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 26:
	Mar 30 14:34:22.888 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:62:1D:46 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:59609 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.200 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x41 0x7D 0xB6 0x18 0x0  0x6A 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 27: 
	Mar 30 14:34:23.740 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:3D:E1 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:43277 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.124 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x38 0x6D 0xBF 0x74 0x0  0x1E 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 28:
	Mar 30 14:34:23.740 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:3D:E1 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:43277 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.124 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x38 0x6D 0xBF 0x74 0x0  0x1E 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 29:
	Mar 30 14:34:23.740 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:3D:E1 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:43277 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.124 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x38 0x6D 0xBF 0x74 0x0  0x1E 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 30:
	Mar 30 14:34:24.381 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst EC:22:80:30:E6:E0 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:56421 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.212.64 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3D 0x93 0xB9 0xF8 0x0  0x74 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 31:
	Mar 30 14:34:24.381 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:8E:F8 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:29164 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.114 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x38 0x80 0xBF 0x61 0x0  0x1E 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 32:
	Mar 30 14:34:25.409 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:62:8F:02 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:32029 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.170 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x37 0xB4 0xC0 0x15 0x0  0x36 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 33:
	Mar 30 14:34:25.409 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:0C:AA Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:59630 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.112 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3D 0x50 0xBA 0x57 0x0  0x58 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 34:
	Mar 30 14:34:25.773 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: High, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 68:72:51:82:19:50 Dst 01:00:5E:00:00:01 Type/Len 0x0800
	Ip: ver:IpVersion4 len:24 tos:192 totLen:32 id:0 fragOffset:0 ttl:1 proto:igmp
	    src: 192.168.160.58 dst: 224.0.0.1 hasIpOptions firstFragment lastFragment
	Remaining data: 
	 0: 0x11 0x64 0xEE 0x9B 0x0  0x0  0x0  0x0  0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x94 0x87 0xE1 0xF9 

	Index 35:
	Mar 30 14:34:25.773 KRAT - RxVlan: 2166, RxPort: Gi2/5
	Priority: Normal, Tag: Dot1Q Tag, Event: Expired Ttl, Flags: 0x40, Size: 64
	Eth: Src 34:0A:33:61:F0:55 Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:38 id:37182 fragOffset:0 ttl:1 proto:icmp
	    src: 172.20.193.92 dst: 80.65.31.199 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x66 0xED 0x8E 0xE  0x0  0x3  0x3  0x1  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0xAD 0x93 0xD0 0xB  

	Index 36:
	Mar 30 14:34:25.901 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src 00:AD:24:2F:39:3B Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x0  0xAD 
	10: 0x24 0x2F 0x39 0x3B 0xC0 0xA8 0x8A 0x7  0xFF 0xFF 
	20: 0xFF 0xFF 0xFF 0xFF 0xC0 0xA8 0x8A 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x7F 0x7E 0xAE 0x7C 

	Index 37:
	Mar 30 14:34:25.901 KRAT - RxVlan: 84, RxPort: Te1/8
	Priority: Normal, Tag: Dot1Q Tag, Event: L3 Glean, Flags: 0x40, Size: 102
	Eth: Src 44:03:A7:E8:FF:FF Dst D0:D0:FD:93:18:FF Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:14934 fragOffset:0 ttl:61 proto:icmp
	    src: 10.253.255.2 dst: 172.23.67.65 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x35 0xD3 0xC2 0x25 0x0  0x7  0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 38:
	Mar 30 14:34:27.417 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:49:89 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:3899 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.153 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x34 0x8A 0xC3 0x3A 0x0  0x3B 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 39:
	Mar 30 14:34:27.417 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:CA:DC Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:40781 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.48 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x36 0x87 0xC1 0x78 0x0  0x0  0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 40:
	Mar 30 14:34:28.401 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:64:09:3F Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:23186 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.188 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x36 0xAC 0xC0 0xDD 0x0  0x76 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 41:
	Mar 30 14:34:28.401 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:F8:6B Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:50995 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.202 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3C 0xD3 0xBA 0xEF 0x0  0x3D 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 42:
	Mar 30 14:34:28.817 KRAT - RxVlan: 2147, RxPort: Te1/6
	Priority: Normal, Tag: Dot1Q Tag, Event: L2 Router, Flags: 0x40, Size: 64
	Eth: Src A8:5E:45:72:93:F4 Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0xA8 0x5E 
	10: 0x45 0x72 0x93 0xF4 0xAC 0x14 0xE3 0xC7 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xAC 0x14 0xE3 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0x98 0xF7 0x9A 0xC1 

	Index 43:
	Mar 30 14:34:28.817 KRAT - RxVlan: 10, RxPort: Te1/6
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 64
	Eth: Src 60:63:4C:8D:8B:20 Dst FF:FF:FF:FF:FF:FF Type/Len 0x0806
	Remaining data: 
	 0: 0x0  0x1  0x8  0x0  0x6  0x4  0x0  0x1  0x60 0x63 
	10: 0x4C 0x8D 0x8B 0x20 0xC0 0xA8 0xD4 0xDD 0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0xC0 0xA8 0xD4 0xFE 0x0  0x0  
	30: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	40: 0x0  0x0  0x0  0x0  0x0  0x0  0xA7 0x30 0x30 0xA8 

	Index 44:
	Mar 30 14:34:28.877 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:C7:4D Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:55258 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.210 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x37 0x12 0xC0 0xCA 0x0  0x23 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 45:
	Mar 30 14:34:28.877 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:61:D9:35 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:32644 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.130 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3D 0x65 0xBA 0x43 0x0  0x57 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 46:
	Mar 30 14:34:31.381 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst F8:F0:82:78:90:CC Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:14458 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.212.185 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x3B 0xC  0xBC 0xAB 0x0  0x48 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 47:
	Mar 30 14:34:31.381 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:59:56 Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:14857 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.85 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x32 0xF1 0xC4 0xB9 0x0  0x55 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	          
	Index 48:
	Mar 30 14:34:31.873 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:F8:2D Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:19307 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.201 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x2E 0x1B 0xC9 0xC4 0x0  0x20 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  

	Index 49:
	Mar 30 14:34:31.873 KRAT - RxVlan: 10, RxPort: Te1/2
	Priority: Medium, Tag: Dot1Q Tag, Event: Sa Miss, Flags: 0x40, Size: 102
	Eth: Src D0:D0:FD:93:18:FF Dst 30:71:B2:63:F8:2D Type/Len 0x0800
	Ip: ver:IpVersion4 len:20 tos:0 totLen:84 id:19307 fragOffset:0 ttl:60 proto:icmp
	    src: 10.253.255.2 dst: 192.168.221.201 firstFragment lastFragment
	Remaining data: 
	 0: 0x8  0x0  0x2E 0x1B 0xC9 0xC4 0x0  0x20 0x0  0x0  
	10: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  
	20: 0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0  0x0 
```

</p>
</details>

## Отключаем дебаг
``` bash
C4900M#no debug all
All possible debugging has been turned off
```

## Изучаем содержимое буфера дебага

```bash
C4900M#show platform cpu packet buffered | c RxVlan: 10                          
Number of lines which match regexp = 912

C4900M#show platform cpu packet buffered | c Sa                       
Number of lines which match regexp = 744

C4900M#show platform cpu packet buffered | c D0:D0:FD:93:18:FF        
Number of lines which match regexp = 1808

C4900M#sh int vlan 10
Vlan2123 is up, line protocol is up , Autostate Enabled
  Hardware is Ethernet SVI, address is d0d0.fd93.18ff (bia d0d0.fd93.18ff)
```

## MAC адрес D0:D0:FD:93:18:FF - является мак адресом самой коробки

<details><summary>C4900M#sh mac address-table add D0:D0:FD:93:18:FF</summary>
<p>

```bash	
	Unicast Entries
	 vlan     mac address     type        protocols               port
	---------+---------------+--------+---------------------+-------------------------
	   9      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  10      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  20      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  30      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  40      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  50      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	  60      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	 110      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	 128      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	 140      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
	 600      d0d0.fd93.18ff    static ip,ipx,assigned,other Switch                     
```

</p>
</details>

## Что по итогу:
<p>	
	1. По диагностики была петля, т.к. по содержимому буфера выдно что летит трафик с mac адресом источника d0d0.fd93.18ff, который является мак адресом самой коробки
</p>
<p>
	2. При приходе такого кадра происходит изучения мак адреса на порту, т.е. кадр попадает на Control plane и мак "источника" добавляется в мак таблицу коммутатора, 
	   но т.к. данный мак является маком коробки, то идет взаимоперезапись таблицы мак адресов и процесс этот идет безустанно... Из за этого соответственно CPU постоянно в полке.
</p>
[Общая документация по troubleshooting c4500, c4900M](https://www.cisco.com/c/ru_ru/support/docs/switches/catalyst-4000-series-switches/65591-cat4500-high-cpu.html) 