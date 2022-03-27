title: cli mikrotik

!!!warning "Disclaimer"
          "В жизни каждого мужчины наступает момент когда он решительно рвет со своим прошлым

          и дрожащей рукою приоткрывает вуаль будущего" (ц) Здравствуйте я Ваша Тётя...
          
          Вот и я по работе добрался таки и до микроты, прасти госпади... )

# Mikrotik cli

[дока по cli](https://jcutrer.com/howto/networking/mikrotik/show-mac-address-table)

## OSPF

```bash
"Посмотреть текущий статус osfp"
"/routing ospf neighbor print"
0 instance=OSPF_2 router-id=1.1.1.1 address=1.1.1.1 interface=vlan14 priority=128 
  dr-address=0.0.0.0 backup-dr-address=0.0.0.0 state="Full" state-changes=4 ls-retransmits=0 ls-requests=0 
  db-summaries=0 adjacency=6w2d17h5m16s 

1 instance=OSPF_2 router-id=2.2.2.2 address=2.2.2.2 interface=vlan18 priority=128 
  dr-address=0.0.0.0 backup-dr-address=0.0.0.0 state="Full" state-changes=5 ls-retransmits=0 ls-requests=0 
  db-summaries=0 adjacency=6w2d17h5m17s 

"Посмотреть настроек osfp"
"routing ospf export"         
  # feb/18/2022 10:28:06 by RouterOS 6.46.8
  # software id = A9JI-S6BB
  #
  # model = CCR1009-7G-1C-1S+
  /routing ospf instance
  set [ find default=yes ] name=OSPF_1 router-id=1.1.1.1
  /routing ospf interface
  add authentication=md5 authentication-key=cowboyMarlboro cost=210 dead-interval=12s hello-interval=3s interface=Tu1 \
      network-type=point-to-point
  add authentication=md5 authentication-key=cowboyMarlboro cost=220 dead-interval=12s disabled=yes hello-interval=3s \
      network-type=point-to-point
  add authentication=md5 authentication-key=cowboyMarlboro cost=220 dead-interval=12s hello-interval=3s interface=Tu2 \
      network-type=point-to-point
  add authentication=md5 authentication-key=cowboyMarlboro cost=20 dead-interval=12s hello-interval=3s interface=vlan14 \
      network-type=point-to-point
  add authentication=md5 authentication-key=cowboyMarlboro dead-interval=12s hello-interval=3s interface=vlan18 \
      network-type=point-to-point
  /routing ospf network
  add area=backbone comment="Loopback net" network=1.1.1.1/32
  add area=backbone comment=to_router1 network=2.2.2.2/30
  add area=backbone comment=to_router2 network=3.3.3.3/30
  add area=backbone comment=OSPF_backup_router1 network=192.168.1.1/30
  add area=backbone comment=OSPF_main_router2 network=192.168.2.1/30
```

### Посмотреть таблицу маршрутизации 
```bash
  ip route print                    
```

### Посмотреть настройки ip адресов на устройстве
```bash
  ip address print        
```

### Посмотреть статус интерфейсов
```bash
  interface ethernet print 
```
### Посмотреть статус и маки интерфейсов
```bash
interface print          
```

### Настройка EoIP
      
Протокол EoIP - обеспечивает построение L2 туннуля между двумя маршрутизаторами микротик.

[Дока1](https://wiki.mikrotik.com/wiki/Manual:Interface/EoIP)

[Дока2](https://asp24.ru/mikrotik/sozdanie-domashney-seti-na-baze-ustroystv-mikrotik-chast-5-sozdanie-eoip-tunnelya/)

```bash
"router1"
interface bridge add comment=router1 name=Bridge-eoip1000

interface eoip add comment=router1 name=eoip1000 remote-address=1.1.1.1 tunnel-id=1000

interface bridge port add bridge=Bridge-eoip1000 interface=eoip1000
interface bridge port add bridge=Bridge-eoip1000 interface=ether2

"router2"
interface bridge add comment=router2 name=bridge1000

interface eoip add comment=router2 name=eoip-tunnel1000 remote-address=2.2.2.2 tunnel-id=1000

interface vlan add comment=router2 interface=sfp4 name=vlan1000 vlan-id=1000

interface bridge port add bridge=bridge1000 interface=vlan1000
interface bridge port add bridge=bridge1000 interface=eoip-tunnel1000

"Посмотреть информацию о статусе туннеля"
interface eoip print
      Flags: X - disabled, R - running 
      0  R ;;; router1
        name="eoip1000" mtu=auto actual-mtu=1438 l2mtu=65535 
        mac-address=FE:0C:DE:AD:BE:EF arp=enabled arp-timeout=auto 
        loop-protect=default loop-protect-status=off 
        loop-protect-send-interval=5s loop-protect-disable-time=5m 
        local-address=0.0.0.0 remote-address=2.2.2.2 tunnel-id=1000 
        keepalive=10s,10 dscp=inherit clamp-tcp-mss=yes 
        dont-fragment=no allow-fast-path=yes 

"Чтобы поменять ip адрес на EoIP туннеле надо изначально знать номер EoIP туннеля"
"просмотреть можно так: interface eoip print"

"Поменять адрес на EoIP туннеле"
interface eoip set name=eoip-tunnel1000 remote-address=3.3.3.3
      numbers: 3
```

