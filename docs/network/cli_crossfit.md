title МойДжентельменскийНабор JuniperHuaweiCiscoBGPcli

# Просмотр соседей 

## Juniper
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
	
				admin@ABK-MBR1> show configuration protocols bgp group EXTERNAL_PEER neighbor x.x.x.x 
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

## Huawei
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

	
## Cisco IOS/IOS-XE
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
	sh run part router bgp 50427
```
	
```bash
	# посмотреть конфиг для всей секции bgp (выполняется много дольше)
	  sh run | sec  router bgp
```

## Cisco IOS-XR

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
			  policy X_in in             		  []
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
