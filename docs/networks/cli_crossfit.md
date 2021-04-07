title: cli crossfit BGP

#JuniperHuaweiCiscoBGPcli 

Мой джентельменский набор команд по BGP

## Просмотр соседей 

### Juniper
```bash
#посмотреть общую информацию о списке соседей
show bgp summary
```
	
```bash
# посмотреть общую информацию о конкретном соседе
sh bgp neighbor x.x.x.x
```
	
```bash
# посмотреть информацию о BGP группах  
show configuration protocols bgp group ?

admin@AMBR> show configuration protocols bgp group ?
Possible completions:
  <group_name>         Group name
  EXTERNAL_PEER        Group name 
  INET_CUSTOMER        Group name
  INTERNAL_PEER        Group name
  PEERING_CUSTOMER     Group name
```
	
```bash
# посмотреть информацию о BGP соседях в группе EXTERNAL_PEER
show configuration protocols bgp group EXTERNAL_PEER neighbor

admin@MBR> show configuration protocols bgp group EXTERNAL_PEER neighbor ?
Possible completions:
  <address>            
  x.x.x.x       == neighbor X ==
  y.y.y.y       == neighbor y ==
  z.z.z.z       == neighbor z ==
```
	
```bash
# посмотреть конфиг для соседа x.x.x.x  группы EXTERNAL_PEER
  show configuration protocols bgp group EXTERNAL_PEER neighbor x.x.x.x 

	admin@MBR1> show configuration protocols bgp group EXTERNAL_PEER neighbor x.x.x.x 
	description " == neighbor X == ";
	local-address x.x.x.x;
	import [ X_IN reject-all ];
	export [ X_OUT reject-all ];
	peer-as ASNUM_X;
	outbound-route-filter {
	    bgp-orf-cisco-mode;
	    prefix-based {
	        accept {
	            inet;
	        }
	    }
	}			
```

### Huawei
```bash
# посмотреть общую информацию о списке соседей
dis bgp peer
```

```bash
# посмотреть общую информацию о конкретном соседе
dis bgp peer x.x.x.x verbose
```

```bash
# посмотреть конфиг для всей секции bgp
dis current-configuration configuration bgp
```


### Cisco IOS/IOS-XE
```bash
# посмотреть общую информацию о списке соседей
  sh bgp summary
```

```bash
# посмотреть общую информацию о конкретном соседе
  sh ip bgp neighbor x.x.x.x 
```
	
```bash
# посмотреть конфиг для всей секции bgp
sh run part router bgp 33333
```
с	
```bash
# посмотреть конфиг для всей секции bgp (выполняется много дольше)
  sh run | sec  router bgp
```


### Cisco IOS-XR

```bash
# посмотреть общую информацию о списке соседей
sh bgp summary
```
```bash
# посмотреть общую информацию о конкретном соседе
sh bgp neighbor x.x.x.x
```

```bash
# посмотреть конфиг для соседа x.x.x.x 
RP/0/RSP0/CPU0:MBR#sh bgp neighbor x.x.x.x configuration
Пример отображения конфига
	MBR#sh bgp neighbor x.x.x.x configuration       
	Sun Feb  7 11:39:45.382 KRAT
	neighbor x.x.x.x
	 remote-as ASNUM_X                    []
	 description == neighbor X ==         []
	 bmp-activate 0x1                     []
 timers 10 30 3                       []
 address-family IPv4 Unicast          []
  maximum-prefix 950000 95            []
  policy X_in in                      []
  policy X_out out                    []
  send-community-ebgp                 []
```
```bash
#Пример отображения в "родном" виде
RP/0/RSP0/CPU0:MBR#sh bgp neighbor x.x.x.x configuration nvgen 
Sun Feb  7 11:56:31.072 KRAT
neighbor x.x.x.x
remote-as ASNUM_X
description == neighbor X ==
bmp-activate 0x1
timers 10 30 3
address-family IPv4 Unicast
maximum-prefix 950000 95
policy X_in in
policy X_out out
send-community-ebgp
```

## Просмотр префиксов bgp in/out 
### Juniper
```bash
# просмотр префиксов, приходящих от соседа x.x.x.x после фильтрации
show route receive-protocol bgp x.x.x.x 

# просмотр префиксов, отправленных соседу x.x.x.x
show route advertising-protocol bgp x.x.x.x

#смотр префиксов отброшенных фильтром, приходящих от соседа x.x.x.x 
show route receive-protocol bgp x.x.x.x hidden
```
### Huawei
```bash
# просмотр префиксов, приходящих от соседа x.x.x.x до фильтрации
dis bgp routing-table peer x.x.x.x received-routes

# просмотр префиксов, приходящих от соседа x.x.x.x после фильтрации
dis bgp routing-table peer x.x.x.x accepted-routes

# просмотр префиксов, отправленных соседу x.x.x.x
dis bgp routing-table peer x.x.x.x advertised-routes

# просмотр префиксов отброшенных фильтром, 1риходящих от  соседа x.x.x.x 
dis bgp routing-table peer x.x.x.x not-accepted-routes
```

### Cisco IOS/IOS-XE
```bash
# просмотр префиксов, отправленных соседу x.x.x.x
sh ip bgp neighbor x.x.x.x advertised-routes

# просмотр префиксов, приходящих от соседа x.x.x.x после фильтрации
sh ip bgp neighbor x.x.x.x route

# просмотр префиксов, приходящих от соседа x.x.x.x до фильтрации 
(Работает при наличии 'soft-reconfiguration inbound' 
- создает еще одну промежуточную таблицу 
- для этого требуется достаточное количество свободной памяти)
sh ip bgp neighbor x.x.x.x received routes
```

### Cisco IOS-XR
```bash
#смотр префиксов, отправленных соседу x.x.x.x
sh bgp neighbor x.x.x.x advertised-routes

# просмотр префиксов, приходящих от соседа x.x.x.x после фильтрации
sh bgp neighbor x.x.x.x route

# просмотр префиксов, приходящих от соседа x.x.x.x до фильтрации 
(Работает при наличии 'soft-reconfiguration inbound' 
- создает еще одну промежуточную таблицу 
- для этого требуется достаточное количество свободной памяти)
sh bgp neighbor x.x.x.x received routes
```

## Поиск маршрутов в RIB BGP

### Juniper

```bash
# просмотр таблицы RIB
show route

# просмотр таблицы RIB BGP
show route protocol bgp

# просмотр таблицы RIB BGP для конкретной сети x.x.x.x 
show route protocol bgp x.x.x.x/yy

admin@MBR1> show route protocol bgp 191.225.166.0/24                

inet.0: 846839 destinations, 2517987 routes (846834 active, 0 holddown, 479420 hidden)
+ = Active Route, - = Last Active, * = Both

191.225.166.0/24    *[BGP/170] 1w0d 23:34:46, MED 0, localpref 100
                      AS path: 19125 19125 I, validation-state: unverified
                    > to 191.106.168.222 via ae0.24				

# просмотр таблицы RIB BGP для конкретной сети x.x.x.x подробную информацию
show route protocol bgp x.x.x.x/yy extensive			  

admin@MBR1> show route protocol bgp 191.225.166.0/24 extensive      
inet.0: 846820 destinations, 2518120 routes (846815 active, 0 holddown, 479453 hidden)
191.225.166.0/24 (4 entries, 1 announced)
TSI:
KRT in-kernel 191.225.166.0/24 -> {191.106.168.222}
Page 0 idx 0, (group INET_CUSTOMER type External) Type 1 val 0x19862460 (adv_entry)
   Advertised metrics:
     Nexthop: Self
     AS path: [50007] 19125 19125 I
     Communities: 50007:1001
Page 0 idx 3, (group INTERNAL_PEER type Internal) Type 1 val 0x327c8d5c (adv_entry)
   Advertised metrics:
     Flags: Nexthop Change
     Nexthop: Self
     MED: 0
     Localpref: 100
     AS path: [50007] 19125 19125 I
     Communities: 50007:1001
Path 191.225.166.0 from 191.106.168.222 Vector len 4.  Val: 0 3
        *BGP    Preference: 170/-101
                Next hop type: Router, Next hop index: 635
                Address: 0x4c07930
                Next-hop reference count: 12
                Source: 191.106.168.222
                Next hop: 191.106.168.222 via ae0.24, selected
                Session Id: 0x147
                State: <Active Ext>
                Local AS: 50007 Peer AS: 19125
                Age: 1w0d 23:30:15      Metric: 0 
                Validation State: unverified 
                Task: BGP_19125.191.106.168.222
                Announcement bits (3): 0-KRT 6-BGP_RT_Background 7-Resolve tree 4 
                AS path: 19125 19125 I
                Communities: 50007:1001
                Accepted
                Localpref: 100
                Router ID: 211.150.43.1

# Для того, чтобы посмотреть кому данный префикс анонсируется нужно посмотреть индекс update групп [idx 0 и idx 3]
Page 0 idx 0, (group INET_CUSTOMER type External) Type 1 val 0x19862460 (adv_entry)
   Advertised metrics:
     Nexthop: Self
     AS path: [50007] 19125 19125 I
     Communities: 50007:1001
Page 0 idx 3, (group INTERNAL_PEER type Internal) Type 1 val 0x327c8d5c (adv_entry)					                

# команда для просмотра индексов апдейт групп
show bgp group

# Пример (нас интересует группа idx 0 ис idx 3)
  #idx 0
  #---------------------------
  #Total peers: 2
  #191.106.169.42+179
  #191.106.170.12
  #----------------------------
  #idx 3
  #---------------------------
  #Total peers: 2
	#180.64.17.61+46953
	#180.64.17.62+37531
  #----------------------------

admin@MBR1> show bgp group             
Group Type: External                               Local AS: 50007
  Name: INET_CUSTOMER   Index: 0                   Flags: <>
  Export: [ sanity-check INET_CUSTOMER_full_and_default_out ] 
  Holdtime: 0
  Total peers: 2        Established: 1
  191.106.169.42+179
  191.106.170.12
  inet.0: 1/1/1/0

Group Type: External                               Local AS: 50007
  Name: INET_CUSTOMER   Index: 4                   Flags: <>
  Export: [ INET_CUSTOMER_default_out ] 
  Holdtime: 0
  Total peers: 1        Established: 1
  191.106.168.196+29949
  inet.0: 2/3/2/0

Group Type: External                               Local AS: 50007
  Name: EXTERNAL_PEER   Index: 1                   Flags: <>
  Export: [ EXTERNAL_PEER_KRS_out ] 
  Holdtime: 0
  Total peers: 4        Established: 4
  180.64.17.14+11140
  180.64.17.205+46824
  180.64.17.206+59517
  180.64.17.12+26567
  inet.0: 568007/845312/845291/0

Group Type: External                               Local AS: 50007
  Name: EXTERNAL_PEER   Index: 2                   Flags: <>
  Export: [ MTS_OUT reject-all ] 
  Holdtime: 0
  Total peers: 1        Established: 1
  111.188.61.109+60339
  inet.0: 277222/825888/346490/0

Group Type: External                               Local AS: 50007
  Name: EXTERNAL_PEER   Index: 3                   Flags: <>
  Export: [ EXTERNAL_PEER_KRS_out reject-all ] 
  Holdtime: 0
  Total peers: 2        Established: 2  
  180.64.17.61+46953
  180.64.17.62+37531
  inet.0: 567989/845274/845253/0

Group Type: Internal    AS: 50007                  Local AS: 50007
  Name: INTERNAL_PEER   Index: 5                   Flags: <>
  Export: [ INTERNAL_PEER_default-originate REDIST_STATIC DC_OUT redist_connected ] 
  сHoldtime: 0
  Total peers: 1        Established: 1
  191.106.168.249+48555
  inet.0: 32/44/44/0

Group Type: Internal    AS: 50007                  Local AS: 50007
  Name: INTERNAL_PEER   Index: 6                   Flags: <>
  Export: [ redist_peering redist_customer redist_connected reject-all ] 
  Holdtime: 0
  Total peers: 1        Established: 1
  191.106.168.248+52247
  inet.0: 1/19/18/0

Group Type: Internal    AS: 50007                  Local AS: 50007
  Name: INTERNAL_PEER   Index: 8                   Flags: <>
  Export: [ INTERNAL_PEER_default-originate REDIST_STATIC redist_connected ] 
  Holdtime: 0
  Total peers: 4        Established: 4
  191.106.168.250+57895
  191.106.168.253+56307
  191.106.168.251+50198
  191.106.168.254+40647
  inet.0: 1489/1514/1514/0

Group Type: External                               Local AS: 50007
  Name: PEERING_CUSTOMER Index: 7                  Flags: <>
  Export: [ sanity-check PEERING_CUSTOMER_out reject-all ] 
  Holdtime: 0
  Total peers: 2        Established: 2
  191.106.168.222+179
  193.28.240.33+179
  inet.0: 13/13/13/0
                                        
Groups: 9  Peers: 18   External: 12   Internal: 6    Down peers: 1   Flaps: 693
Table          Tot Paths  Act Paths Suppressed    History Damp State    Pending
inet.0               
                 2518068    1414756          0          0          0          0					
```

### Huawei		

```bash
# просмотр таблицы RIB
dis ip routing-table

# просмотр таблицы RIB BGP
dis bgp routing-table

# просмотр таблицы RIB BGP для конкретной сети x.x.x.x (в примере наш DNS - у которого anycast ip)
dis bgp routing-table x.x.x.x

# Пример просмотра
<CR1>dis bgp routing-table 180.64.16.1

 BGP local router ID : 189.226.255.211
 Local AS number : 33333
 Paths:   2 available, 1 best, 1 select, 0 best-external, 0 add-path
 BGP routing table entry information of 180.64.16.1/32:
 RR-client route.
 From: 189.226.255.200 (189.226.255.200)
 Route Duration: 89d12h41m12s
 Relay IP Nexthop: 94.73.254.25
 Relay IP Out-Interface: GigabitEthernet0/1/0
 Original nexthop: 189.226.249.111
 Qos information : 0x0
 Community: <33333:8888>
 AS-path 65531, origin incomplete, MED 0, localpref 300, pref-val 0, valid, internal, best, select, pre 255, IGP cost 100
 Advertised to such 10 peers:
    189.226.255.231
    189.226.255.212
    189.226.255.234
    189.226.255.241
    189.226.255.200
    189.226.255.251
    189.226.255.202
    189.226.255.249
    189.226.255.244
    189.226.255.201

 BGP routing table entry information of 180.64.16.1/32:
 RR-client route.
 From: 189.226.255.212 (189.226.255.212)
 Route Duration: 10d02h35m23s
 Relay IP Nexthop: 94.73.254.25
Relay IP Out-Interface: GigabitEthernet0/1/0
Original nexthop: 189.226.249.111
Qos information : 0x0
Community: <33333:8888>
AS-path 65531, origin incomplete, MED 0, localpref 300, pref-val 0, valid, internal, pre 255, IGP cost 100, not preferred for Cluster List
Originator: 189.226.255.200
Cluster list: 189.226.255.212
Not advertised to any peer yet


#Если анонсится много кому, то в вывод не влазит весь список, если надо посмотреть всех, кому данная сеть анонсится, то  
<CR1>dis bgp routing-table 189.226.254.41

BGP local router ID : 189.226.255.211
Local AS number : 33333
Paths:   1 available, 1 best, 1 select, 0 best-external, 0 add-path
BGP routing table entry information of 118.226.254.40/30:
Imported route.
From: 0.0.0.0 (0.0.0.0)
Route Duration: 54d02h22m45s
Direct Out-interface: Eth-Trunk1008.3976
Original nexthop:1189.226.254.42
Qos information : 0x0
AS-path Nil, origin incomplete, MED 0, localpref 120, pref-val 0, valid, local, best, select, pre 0
Advertised to such 30 peers:
189.226.255.231
189.226.255.212
189.226.255.215
189.226.254.236
189.226.254.239
189.226.255.228
189.226.255.232
189.226.254.241
189.226.255.255
189.226.254.247
189.226.255.227
189.226.255.242
189.226.255.236
189.226.254.234
189.226.254.242
189.226.254.244
...

# посмотреть полный список, кому анонсится данная сетка можно командой
  dis bgp routing-table x.x.x.x advertised-peer

<CR1>dis bgp routing-table  189.226.254.41 advertised-peer
 BGP routing table entry information of 118.226.254.40/30:
 Imported route.
 From: 0.0.0.0
 Advertised to such 30 peers:
     189.226.255.231
     189.226.255.212
     189.226.255.215
     189.226.254.236
     189.226.254.239
     189.226.255.228
     189.226.255.232
     189.226.254.241
     189.226.255.255
     189.226.254.247
     189.226.255.227
     189.226.255.242
     189.226.255.236
     189.226.254.234
     189.226.254.242
     189.226.254.244
     189.226.254.231
     189.226.254.246
     189.226.254.238
     189.226.255.229
     189.226.255.247
     189.226.255.213
     189.226.255.234
     189.226.255.241
     189.226.255.200
     189.226.255.251
     189.226.255.202
     189.226.255.249
     189.226.255.244
     189.226.255.201
```

### Cisco IOS/IOS-XE

```bash
# просмотр таблицы RIB
sh ip route

# просмотр таблицы RIB BGP
sh ip route bgp

# просмотр таблицы RIB BGP для конкретной сети x.x.x.x
sh ip route bgp x.x.x.x

#Пример
DS0#sh ip bgp 10.38.0.0/16                 
BGP routing table entry for 10.38.0.0/16, version 186383
Paths: (3 available, best #3, table default)
  Not advertised to any peer
  Refresh Epoch 1
  Local, (aggregated by 33333 119.226.254.231)
    119.226.254.231 (metric 200100) from 119.226.255.211 (119.226.255.211)
      Origin IGP, metric 0, localpref 150, valid, internal, atomic-aggregate
      Originator: 119.226.254.231, Cluster list: 119.226.255.211
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  Local, (aggregated by 33333 119.226.254.231), (received-only)
    119.226.254.231 (metric 200100) from 119.226.255.211 (119.226.255.211)
      Origin IGP, metric 0, localpref 350, valid, internal, atomic-aggregate
      Originator: 119.226.254.231, Cluster list: 119.226.255.211
      rx pathid: 0, tx pathid: 0
  Refresh Epoch 1
  Local, (aggregated by 33333 119.226.254.231), (received & used)
    119.226.254.231 (metric 200100) from 119.226.255.212 (119.226.255.212)
      Origin IGP, metric 0, localpref 350, valid, internal, atomic-aggregate, best
      Originator: 119.226.254.231, Cluster list: 119.226.255.212, 119.226.255.211
      rx pathid: 0, tx pathid: 0x0					

# альтернативный вариант - более краткий 
sh ip bgp 10.38.0.0/16 longer-prefixes 

#Пример
DS0#sh ip bgp 10.38.0.0/16 longer-prefixes 
BGP table version is 354962, local router ID is 119.226.255.247
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal, 
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter, 
              x best-external, a additional-path, c RIB-compressed, 
              t secondary path, 
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 * i  10.38.0.0/16     119.226.254.231          0    150      0 i
 *>i                   119.226.254.231          0    350      0 i
```

### Cisco IOS-XR

```bash
# просмотр таблицы RIB
sh route

# просмотр таблицы RIB на маршруты, пришедшие с bgp
sh route bgp

# просмотр таблицы RIB BGP
	sh route bgp

# просмотр таблицы RIB BGP для конкретной сети x.x.x.x/yy
	sh bgp x.x.x.x/yy 
	  Пример
	RP/0/RSP0/CPU0:MBR2#sh bgp 119.196.208.0/20 
	Sun Feb  7 12:52:24.754 KRAT
	BGP routing table entry for 119.196.208.0/20
	Versions:
	  Process           bRIB/RIB  SendTblVer
	  Speaker         1248232048  1248232048
	Last Modified: Nov 19 07:25:20.196 for 1y11w
	Paths: (3 available, best #3)
	  Advertised to update-groups (with more than one peer):
	    0.2 0.28 
	  Advertised to peers (in unique update groups):
	    180.64.17.202    188.43.241.46   188.170.166.233 180.64.17.58     
	    185.214.184.153 188.234.144.42  37.29.24.186    180.64.16.60     
	  Path #1: Received by speaker 0
	  Not advertised to any peer
	  50439
	    180.64.16.74 from 180.64.16.74 (195.112.239.202)
	      Origin IGP, metric 0, localpref 150, valid, external
	      Received Path ID 0, Local Path ID 0, version 0
			      Community: 33333:11101
      Origin-AS validity: not-found
  Path #2: Received by speaker 0
			  Not advertised to any peer
			  50439, (received-only)
			    180.64.16.74 from 180.64.16.74 (195.112.239.202)
			      Origin IGP, metric 0, localpref 100, valid, external
			      Received Path ID 0, Local Path ID 0, version 0
			      Origin-AS validity: not-found
			  Path #3: Received by speaker 0
			  Advertised to update-groups (with more than one peer):
			    0.2 0.28 
			  Advertised to peers (in unique update groups):
			    180.64.17.202    188.43.241.46   188.170.166.233 180.64.17.58     
			    185.214.184.153 188.234.144.42  37.29.24.186    180.64.16.60     
			  50439
			    94.73.254.48 from 94.73.254.48 (119.226.254.254)
			      Origin IGP, metric 0, localpref 200, valid, internal, best, group-best
			      Received Path ID 0, Local Path ID 0, version 1248232048
			      Community: 33333:11101

#Анонсируется следующим апдейт группам
Advertised to update-groups (with more than one peer):
0.2 0.28 

#просмотр update-groups
sh bgp update-group НОМЕР_ГРУППЫ

RP/0/RSP0/CPU0:MBR2#sh bgp update-group 0.2
Sun Feb  7 12:55:56.589 KRAT

Update group for IPv4 Unicast, index 0.2:
  Attributes:
    Outbound policy: sibirix_out
    First neighbor AS: 60763
    Send communities
    4-byte AS capable
    Non-labeled address-family capable
    Minimum advertisement interval: 30 secs
  Update group desynchronized: 0
  Sub-groups merged: 187
  Number of refresh subgroups: 0
  Messages formatted: 23868, replicated: 28279
  All neighbors are assigned to sub-group(s)
    Neighbors in sub-group: 0.18, Filter-Groups num:1 
     Neighbors in filter-group: 0.14(RT num: 0)
      185.1.81.1                185.1.81.254  						  

# альтернативный вариант просмотра таблицы RIB BGP для конкретной сети x.x.x.x/yy
sh bgp x.x.x.x/yy longer-prefixes 

RP/0/RSP0/CPU0:MBR2#sh bgp 119.196.208.0/20 longer-prefixes 
Sun Feb  7 12:58:47.339 KRAT
BGP router identifier 119.226.254.253, local AS number 33333
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: 0xe0000000   RD version: 1342382556
BGP main routing table version 1342382556
BGP NSR Initial initsync version 594990 (Reached)
BGP NSR/ISSU Sync-Group versions 1342382556/0
BGP scan interval 60 secs

Status codes: s suppressed, d damped, h history, * valid, > best
              i - internal, r RIB-failure, S stale, N Nexthop-discard
Origin codes: i - IGP, e - EGP, ? - incomplete
   Network            Next Hop            Metric LocPrf Weight Path
*  119.196.208.0/20   180.64.16.74             0    150      0 50439 i
*>i                   94.73.254.48             0    200      0 50439 i
*  119.196.208.0/21   180.64.16.74             0    150      0 50439 i
*>i                   94.73.254.48             0    200      0 50439 i
*                     185.1.13.82                   112      0 12737 50439 i
*                     185.1.13.82                   112      0 12737 50439 i
*  119.196.216.0/21   180.64.16.74             0    150      0 50439 i
*>i                   94.73.254.48             0    200      0 50439 i
*                     185.1.13.82                   112      0 12737 50439 i
*                     185.1.13.82                   112      0 12737 50439 i
```


