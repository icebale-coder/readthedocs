title: NetEngine 8000

# Huawei NetEngine 8000

## Конфигурация

### Базовая

```bash
clock timezone $NAME add HH:MM:SS # задать временную зону
set save-configuration interval MM # сохранять current-config раз в MM минут
```

### Управление конфигурацией

* `display configuration candidate changes` (`di co ca ch`) - показать различия между текущей и candidate(незакоммиченной) конфигурацией

* `refresh configuration candidate` (`re co ca`) - если конфигурация была изменена во время конфигурирования, обновить измененный конфиг

* `clear configuration candidate` (`c c c`) - очистить изменённую, но незакомиченную конфигурацию

### Интерфейсы

#### Port-Group

!!! note "Использовать крайне неудобно"

Аналог interface range, можно только `shutdown`/`undo shutdown`

```bash
# Создаём временную группу
port-group group-member $INT1 to $INT2 $INT3 to $INT4
  shutdown

# Создаём постоянную группу
port-group $GRNAME
  group-member $INT1 to $INT2 $INT3 to $INT4
```

#### Ether-Trunk

Аналог PortChannel у Cisco

```bash
# Создаём сам интерфейс
interface eth-trunk $ID
  portswitch				# перевести в L2 режим (не обязательно, естественно)
  mode lacp-static			# включаем LACP
  load-balance {packet-all|src-dst-ip|src-dst-mac|symmetric-hash}
  statistic enable			# без этого не включаются counter'ы

# Добавление интерфейсов в ET
interface eth-trunk $ID
  trunkport $INT1 to $INT2 mode active
# или
interface $INT1
  eth-trunk $ID mode active
interface $INT2
  eth-trunk $ID mode active
```

#### Subinterface

```bash
# Создание интерфейса
interface Eth-Trunk $ID.$VID
  vlan-type dot1q $VID
  statistic enable # вкл стат загруженности
```

### Маршрутизация

#### BGP

```bash
# Создание процесса BGP
bgp $ASN
 router-id $RID
 # Добавление peer'а
 peer $IPADDR as-number $PEERASN
 peer $IPADDR description $DESCR
 peer $IPADDR connect-interface $INT # source-interface
 peer $IPADDR ignore # временное отключение сессии с peer'ом
 ipv4-family unicast
  # Указание максимального кол-ва маршрутов с одинаковой ценой
  maximum load-balancing {ebgp|ibgp) $NUM
  peer $IPADDR reflect-client # настройка peer'а как RR-client
  peer $IPADDR next-hop-local # смена IP next-hop в маршруте на собственный адрес устройства
  peer $IPADDR advertise-community
  peer $IPADDR route-filter $XPL-RF {export|import} # XPL route-filter на peer'а
  
```

##### Route redistribution

```bash
bgp $ASN
 ipv4-family unicast
  import-route direct route-policy $POLICY
```

#### IS-IS

```bash
# Создание процесса IS-IS
isis $NUM
 description $DESCR
 is-level {level-1|level-1-2|level-2} # установка уровня устройства
 cost-style {narrow|compatible|wide|...}
 timer lsp-generation 5 1 50 level-1
 timer lsp-generation 5 1 50 level-2
 flash-flood 15 level-1
 flash-flood 15 level-2
 bfd all-interfaces enable
 bfd all-interfaces min-tx-interval 50 min-rx-interval 50
 circuit-cost 100000 level-2
 network-entity 49.0001.1092.2625.5214.00
 is-name LAB-DS-NE8000
 timer spf 5 1 50
 frr
  loop-free-alternate level-2
  ti-lfa level-2
```

Привязка IS-IS процесса к интерфейсу:

```bash
interface 25GE0/1/47.1105
 mtu 9198
 description << LAB-C4900M >>
 ip address 94.73.254.42 255.255.255.254
 statistic enable
 pim sm         
 isis enable 1
 isis circuit-type p2p
 isis circuit-level level-2
 isis authentication-mode md5 plain t,fkfqrf2019
 isis cost 100000 level-2
```



#### VPN-instance

Аналог VRF у Cisco

```bash
# Создание VPN-I
ip vpn-instance $NAME
  description $DESCR
  
# Привязка интерфейса к VPN-I
int $INT
  ip binding vpn-instance $NAME
```

#### XPL

При конфигурировании XPL есть два способа их изменения:

* Paragraph-by-paragraph - неинтерактивное изменение политики как текстового файла во встроенном текстовом редакторе (vim):

	```bash
	# Вход в режим конфигурирования Paragraph-by-paragraph
	<NE8000>edit xpl route-filter RF-NET_NAT
	
	# Комбинации в текстовом редакторе:
	:q # выход при отсуствии изменений
	:q! # выход без сохранения внесённых изменений
	:wq # выход с сохранением внесённых изменений
	i # вход в режим редактирования текста
	Esc # выход из режима редактирования текста
	```

* Line-by-line - интерактивное изменение политики в командной строке

##### Route-filter

```bash
# Создание route-filter
xpl route-filter $NAME
 if ... then
  apply ...
  {approve|break|finish|refuse}
  endif
 ...
 end-filter
```

#### IP-prefix

Аналог prefix-list Cisco

```bash
ip ip-prefix $NAME [ index $SEQ ] { permit|deny } $IPADD $MASK [ greater-equal $GE ] [ less-equal $LE ]

ip ip-prefix netMBR index 10 permit 80.65.16.0 20 gr 24 le 31
```

#### Route-policy

Аналог route-map Cisco

```bash
# Создание route-policy
route-policy $NAME { permit|deny } node $SEQ
 # Установить определенный параметр
 apply $PARAM
 # Установить условие, при котором будут применяться параметры
 if-match $MATCH

route-policy LocPrf_90 permit node 10
  apply local-preference 90
  if-match as-path length 2
```

#### Community-filter

##### Advanced

Аналог community-list Cisco, позволяет создавать именованные листы

```bash
ip community-filter advanced deny_as index 10 deny "31257:5000"
ip community-filter advanced deny_as index 20 permit ".*"
```

### Разное

#### Port-mirroring

Конфигурирование интерфейса, в который осуществлять мирроринг:

```bash
interface $INTERFACE
 port-observing observe-index $INDX [ without-filter ]
 # without-filter - отправлять трафик на инт без фильтров
```

Конфигурирование интерфейса, с которого нужно снимать трафик:

```bash
interface $INTERFACE
 port-mirroring { inbound|outbound } [ cpu-packet ] [ pe-vid ] [ user-defined-filter ] [ vlan ]
 # inbound|outbound - направление трафика для мирроринга
 # cpu-packet - миррорить только прилетающий на CPU траффик
 # pe-vid
 # user-defined-filter - миррорить только трафик, указанный с помощью фильтра:
 ## observe user-defined-filter...
 port-mirroring to observe-index $INDX
 # миррорить трафик на интерфейс с $INDX
 port-mirroring car cir $CIR pir $PIR cbs $CBS pbs $PBS
 # Committed Access Rate:
 ## cir - Committed information rate
 ## pir - Peak information rate
 ## cbs - Committed burst size
 ## pbs - Peak burst size
 # Значения указываются в kbps
```

## Известные проблемы

## Описание платформы

### Варианты аппаратного исполнения

Иметь дело довелось со следующими:

#### Huawei NetEngine 8000 F1A-8H20Q

Портовая ёмкость:

* **28** x 1G / 10G
* **20** x 1G / 10G / 25G
* **8** x 40G / 50G / 100G / breakout 4x10G / breakout 4x25G

Спецификация на CR8B0BKP03C0:

| **Item**                                                     | **Specification**                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Cabinet installation standards                               | IEC 19-inch                                                  |
| Dimensions without packaging (H x W x D) [mm(in.)]           | 43.6 mm x 442 mm x 420 mm (1.72 in. x 17.4 in. x 16.54 in.)  |
| Dimensions with packaging (H x W x D) [mm(in.)]              | 175 mm x 550 mm x 650 mm (6.89 in. x 21.65 in. x 25.59 in.)  |
| Chassis height [U]                                           | 1 U                                                          |
| Weight without packaging (base configuration) [kg(lb)]       | 6.3 kg (13.89 lb)                                            |
| Weight without packaging (full configuration) [kg(lb)]       | DC: 8.4 kg(18.52 lb)AC: 8.75 kg(19.29 lb)                    |
| Weight with packaging (full configuration) [kg(lb)]          | DC: 13.58 kg(29.94 lb)AC: 13.75 kg(30.31 lb)                 |
| Typical power consumption [W]                                | 325 W                                                        |
| Typical heat dissipation [BTU/hour]                          | 1054.44 BTU/hour                                             |
| MTBF [year]                                                  | DC: 25.52AC: 25.65                                           |
| MTTR [hour]                                                  | 0.5 hour                                                     |
| Availability                                                 | 0.99999                                                      |
| CPU                                                          | 20-core 2.0 GHz                                              |
| SDRAM                                                        | 16 GB                                                        |
| Storage                                                      | 4G NAND FLASH                                                |
| Power supply mode                                            | DC,AC/HVDC                                                   |
| Rated input voltage [V]                                      | DC: -48 V/-60 VAC: 100 V to 240 V AC, support 240 V HVDC     |
| Input voltage range [V]                                      | DC: -40 V to -72 VAC: 90V to 290V                            |
| Maximum input current [A]                                    | DC: 30 AAC: 8 A                                              |
| Rated output power [W]                                       | DC: 1000 WAC: 600 W                                          |
| Maximum input cable diameter [mm²]                           | DC:4mm^2 (1-14 meters), 6mm^2 (15-21 meters), 10mm^2 (22-35 meters)AC:2.5mm^2 |
| Front-end circuit breaker/fuse [A]                           | DC:≥32AAC:≥10A                                               |
| Heat dissipation                                             | Air cooling                                                  |
| Airflow direction                                            | Front to back: port-side intake                              |
| Noise at normal temperature (acoustic power) [dB(A)]         | < 72 dB (meeting the ETSI 72 dBA standard)                   |
| Number of slots                                              | 7                                                            |
| Number of service board slots                                | 1                                                            |
| Switching capacity                                           | 2.4Tbit/s                                                    |
| Redundant power supply                                       | 1+1                                                          |
| Redundant fans                                               | 3+1,Short-term running when a single fan becomes invalid at the ambient temperature of 40°C (104°F) |
| Long-term operating temperature [°C(°F)]                     | Port-side Intake:-5°C ~ 45°C(23°F to 113°F)                  |
| Restriction on the operating temperature variation rate [°C(°F)] | ≤ 30°C/hour (86°F/hour)                                      |
| Storage temperature [°C(°F)]                                 | -40°C ~ 70°C(-40°F to 158°F)                                 |
| Long-term operating relative humidity [RH]                   | 5% to 90% RH, non-condensing                                 |
  | Short-term operating relative humidity [RH]                  | 5% to 95% RH, non-condensing                                 |
  | Storage relative humidity [RH]                               | 5% to 95% RH, non-condensing                                 |
  | Long-term operating altitude [m(ft.)]                        | < 4000 m(< 13123.2 ft.)                                      |
  | Storage altitude [m(ft.)]                                    | < 5000 m(< 16404.2 ft.)                                      |
  | RTU supported                                                | Yes.<br/>82402000: NetEngine 8000 F1A-8H20Q Fixed Port 100GE Upgrade RTU<br/>82402001: NetEngine 8000 F1A-8H20Q Fixed Port 25GE Upgrade RTU<br/>82402002: NetEngine 8000 F1A-8H20Q Fixed Port 10GE Upgrade RTU<br/>For details about how to use the RTU, see "Installation >License Usage Guide". |

### Версии ПО

#### V800R012C00SPC300

В данной версии проблем обнаружено не было.