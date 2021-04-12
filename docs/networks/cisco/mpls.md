title: MPLS Cisco cli

# Команды для просмотра информации по MPLS

## Просмотр инфы по MPLS общие

### Просмотр информации об LDP соседях
<details><summary>show mpls ldp neighbor</summary>
<p>

```bash
Peer LDP Ident: 2.2.2.2:0; Local LDP Ident 1.1.1.1:0
    TCP connection: 2.2.2.2.15423 - 1.1.1.1.646
    State: Oper; Msgs sent/rcvd: 1418/1415; Downstream
    Up time: 20:28:26
    LDP discovery sources:
      FastEthernet0/1, Src IP addr: 10.1.2.2
    Addresses bound to peer LDP Ident:
      10.1.2.2        2.2.2.2
Peer LDP Ident: 3.3.3.3:0; Local LDP Ident 1.1.1.1:0
    TCP connection: 3.3.3.3.11567 - 1.1.1.1.646
    State: Oper; Msgs sent/rcvd: 1422/1422; Downstream
    Up time: 20:28:26
    LDP discovery sources:
      FastEthernet0/0, Src IP addr: 10.1.3.3
    Addresses bound to peer LDP Ident:
      10.3.4.3        3.3.3.3         10.1.3.3
```
</p>
</details>


### Показать таблицу форвардинга меток
<details><summary>show mpls forwarding-table</summary>
<p>

```bash
Local  Outgoing    Prefix            Bytes tag  Outgoing   Next Hop
tag    tag or VC   or Tunnel Id      switched   interface
16     Pop tag     3.3.3.3/32        0          Fa0/0      10.1.3.3
17     Pop tag     2.2.2.2/32        0          Fa0/1      10.1.2.2
18     18          4.4.4.4/32        0          Fa0/0      10.1.3.3
19     19          5.5.5.5/32        0          Fa0/0      10.1.3.3
20     20          6.6.6.6/32        0          Fa0/0      10.1.3.3
21     Pop tag     10.3.4.0/24       0          Fa0/0      10.1.3.3
22     22          10.2.5.0/24       0          Fa0/0      10.1.3.3
23     23          10.4.5.0/24       0          Fa0/0      10.1.3.3
24     24          10.5.6.0/24       0          Fa0/0      10.1.3.3
25     Untagged    10.10.10.10/32[V] 0          Fa1/0      192.168.1.1
26     Aggregate   192.168.1.0/24[V] 0
27     Aggregate   192.168.1.0/24[V] 0
28     Untagged    11.11.11.11/32[V] 0          Fa2/0      192.168.1.1
```
</p>
</details>

### Просмотр транспортной метки для FEC
<details><summary>sh mpls ip binding x.x.x.x yz</summary>
<p>

```bash
# Просмотр транспортной метки для FEC 4.4.4.4
sh mpls ip binding 4.4.4.4 32
   4.4.4.4/32
    in label:     18
    out label:    18        lsr: 2.2.2.2:0
    out label:    18        lsr: 3.3.3.3:0        inuse
```
</p>
</details>


## Просмотр инфы по L3VPN 

### Просмотреть инфу в таблице BGP для определенного VRF и для определееного адреса
<details><summary>sh ip bgp vpnv4 vrf VRFNAME x.x.x.x</summary>
<p>

```bash
# Просмотреть инфу в таблице BGP для VRF C3PO для адреса 192.168.222.0
sh ip bgp vpnv4 vrf C3PO 192.168.222.0
	BGP routing table entry for 64500:100:192.168.222.0/24, version 13
	Paths: (1 available, best #1, table C3PO)
	  Not advertised to any peer
	  64222, imported path from 64222:300:192.168.222.0/24
	    4.4.4.4 (metric 30) from 4.4.4.4 (4.4.4.4)
	      Origin incomplete, metric 0, localpref 100, valid, internal, best
	      Extended Community: RT:64500:300
	      mpls labels in/out nolabel/26
```
</p>
</details>

### Просмотр сервисной и транспортной метки в cef 
<details><summary>sh ip cef vrf VRFNAME x.x.x.x </summary>
<p>

```bash
# Просмотр сервисной и транспортной метки в cef для 20.20.20.20
sh ip cef vrf C3PO 20.20.20.20
	20.20.20.20/32, version 8, epoch 0, cached adjacency 10.1.3.3
	0 packets, 0 bytes
	  tag information set
	    local tag: VPN-route-head
	    fast tag rewrite with Fa0/0, 10.1.3.3, tags imposed: {20 26}
	  via 6.6.6.6, 0 dependencies, recursive
	    next hop 10.1.3.3, FastEthernet0/0 via 6.6.6.6/32
	    valid cached adjacency
	    tag rewrite with Fa0/0, 10.1.3.3, tags imposed: {20 26}
```
</p>
</details>

## Просмотр инфвы по L2VPN xConnect

### Просмотр всех xconnect-ов
<details><summary>show xconnect all</summary></details>

### Просмотр детальной информации о vc в l2vpn
<details><summary>show mpls l2transport vc detail</summary></details>

### Просмотр детальной информации о vc в l2vpn (по сути аналог предыдущей команды)
<details><summary>show l2vpn atom vc detail</summary></details>
