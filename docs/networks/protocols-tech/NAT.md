title: NAT

# Network Address Translation

https://habr.com/ru/company/vasexperts/blog/316272/

https://ru.wikipedia.org/wiki/NAT

https://vasexperts.ru/blog/kogda-zakonchatsya-adresa-ipv4-cgnat/

https://www.smart-soft.ru/blog/tehnologija_preobrazovanija_setevyh_adresov_nat/

NAT- (Network Address Translation)- Технология трансляции сетевых адресов

NAT изначально был необходим, чтобы использовать ограниченное число публичных адресов ipv4 для доступа в интернет  
кустройствам локальных сетей LAN.

По сути NAT, это по сути, преобразование приватных ipv4 адресов в публичные адреса ipv4, для осуществления, как правило, связности с интернетом.

Хотя есть ньюансы (как из одного анекдота про Петьку и Василия Иваныча)...
```bash
Знавал я локальную сеть... которая состояла почему-то из адресов, 
принадлежащих чужой публичной сети другой страны...
и эта сеть NAT-илась в инетик чз валидный белый ip.
Тут "прямота рук" админа, который это организовывал просто зашкаливала,
а потом уже это легаси выпиливать никто почему то не хотел...
Весь инетик работал, собстно гря, кроме небольшого кусочка ипов из Франции...
"Заходишь так в локалку  - и ощущаешь себя так, как будто попал во Францию, 
ну положим в Прованс, а почему бы и да?!... гыгы! )"
```
Нуу.. это лирика - теперь физика...

### Конценпции NAT

#### 1. Статический NAT (Static NAT)
```bash
"Статический NAT" - трансляция внутреннего адреса во внешние один к одному, 
можно обращаться к публичному адресу на определенный порт и проваливаешься 
на внутренний адрес на этот же порт.

Cone NAT, Full Cone NAT - однозначная (взаимная) трансляция между парами 
«внутренний адрес: внутренний порт» и «публичный адрес: публичный порт». 
Любой внешний хост может инициировать соединение с внутренним хостом 
(если это разрешено в правилах межсетевого экрана).
```

#### 2. Динамический NAT (Dynamic NAT)

```bash
Динамический NAT -  транслирует один внутренний адрес на внешний из предоставленного пула.

Тип динамического NAT работает с пулом публичных адресов, 
которые назначаются на основе принципа «первым пришел, первым обслужен». 
При запросе внутренним устройством доступа к интернету динамическим NAT производится назначение из пула 
публичных ipv4 адресов . Как и в случае со статическим, 
для динамического типа NAT необходимо достаточное число общедоступных адресов, 
чтобы удовлетворить совокупное число одновременных пользовательских сеансов.
```

#### 3. Перегруженный NAT (NAT Overload)
```bash

"NAT Overload" - когда, несколько внутренних адресов транслируется в один внешний 
или в один ip выбираемый из пула адресов.

https://www.k-max.name/linux/netfilter-iptables-v-linux/

Классическая модель, например, для Linux систем, когда имеется один публичный ipv4 
или пул публичных ipv4 адресов с помощью iptables, 
а конкретно таблицы NAT трансляции iptables "table nat" 
происходит трансляция локальных ip адресов локальной сети.
```

В отдельной таблице хранятся состояния соединений.

<details><summary>Посмотреть их можно командой conntrack </summary>
<p>

```bash

conntrack -L | more
tcp      6 49797 ESTABLISHED src=121.4.228.87 dst=109.196.210.39 sport=39324 dport=22 packets=1 bytes=52 [UNREPLIED] src=109.196.210.39 dst=121.4.228.87 sport=22 dport=39324 packets=0 bytes=0 mark=0 use=1
udp      17 7 src=192.168.2.26 dst=8.8.8.8 sport=65055 dport=53 packets=1 bytes=56 src=8.8.8.8 dst=109.196.210.39 sport=53 dport=65055 packets=1 bytes=92 mark=0 use=1
tcp      6 431999 ESTABLISHED src=192.168.2.15 dst=185.5.137.179 sport=55683 dport=443 packets=17 bytes=2905 src=185.5.137.179 dst=109.196.210.39 sport=443 dport=55683 packets=10 bytes=3289 [ASSURED] mark=0 use=1
tcp      6 66 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51611 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51611 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 113 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=52184 dport=3306 packets=10 bytes=679 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=52184 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 42 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51337 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51337 packets=8 bytes=1152 [ASSURED] mark=0 use=1
udp      17 108 src=192.168.2.149 dst=173.194.222.154 sport=60188 dport=443 packets=8 bytes=3703 src=173.194.222.154 dst=109.196.210.39 sport=443 dport=60188 packets=7 bytes=2947 [ASSURED] mark=0 use=1
tcp      6 69 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51660 dport=3306 packets=10 bytes=681 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51660 packets=9 bytes=1204 [ASSURED] mark=0 use=1
tcp      6 4 TIME_WAIT src=115.231.83.77 dst=109.196.210.39 sport=60194 dport=22 packets=14 bytes=2216 src=109.196.210.39 dst=115.231.83.77 sport=22 dport=60194 packets=13 bytes=2476 [ASSURED] mark=0 use=1
tcp      6 43 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51342 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51342 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 34 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51235 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51235 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 108437 ESTABLISHED src=49.234.27.210 dst=109.196.210.39 sport=37758 dport=22 packets=1 bytes=52 [UNREPLIED] src=109.196.210.39 dst=49.234.27.210 sport=22 dport=37758 packets=0 bytes=0 mark=0 use=1
tcp      6 44 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51352 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51352 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 34341 ESTABLISHED src=1.15.106.44 dst=109.196.210.39 sport=33748 dport=22 packets=1 bytes=64 [UNREPLIED] src=109.196.210.39 dst=1.15.106.44 sport=22 dport=33748 packets=0 bytes=0 mark=0 use=1
tcp      6 89 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51890 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51890 packets=9 bytes=1204 [ASSURED] mark=0 use=1
tcp      6 89508 ESTABLISHED src=94.191.62.195 dst=109.196.210.39 sport=39960 dport=22 packets=1 bytes=52 [UNREPLIED] src=109.196.210.39 dst=94.191.62.195 sport=22 dport=39960 packets=0 bytes=0 mark=0 use=1
tcp      6 21 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51084 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51084 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 28 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51175 dport=3306 packets=10 bytes=679 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51175 packets=8 bytes=1152 [ASSURED] mark=0 use=2
tcp      6 101 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=52049 dport=3306 packets=10 bytes=684 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=52049 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 57 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51523 dport=3306 packets=10 bytes=678 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51523 packets=9 bytes=1204 [ASSURED] mark=0 use=1
tcp      6 98 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51999 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51999 packets=8 bytes=1152 [ASSURED] mark=0 use=1
tcp      6 305297 ESTABLISHED src=119.45.239.75 dst=109.196.210.39 sport=58954 dport=22 packets=1 bytes=64 [UNREPLIED] src=109.196.210.39 dst=119.45.239.75 sport=22 dport=58954 packets=0 bytes=0 mark=0 use=1
tcp      6 31 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51192 dport=3306 packets=10 bytes=683 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51192 packets=9 bytes=1204 [ASSURED] mark=0 use=1
tcp      6 105 TIME_WAIT src=115.231.83.77 dst=109.196.210.39 sport=33234 dport=22 packets=14 bytes=2216 src=109.196.210.39 dst=115.231.83.77 sport=22 dport=33234 packets=13 bytes=2476 [ASSURED] mark=0 use=1
tcp      6 18 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51052 dport=3306 packets=10 bytes=916 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51052 packets=8 bytes=559 [ASSURED] mark=0 use=1
udp      17 29 src=109.196.210.39 dst=83.234.188.3 sport=50767 dport=53 packets=1 bytes=72 src=83.234.188.3 dst=109.196.210.39 sport=53 dport=50767 packets=1 bytes=129 mark=0 use=1
tcp      6 42 TIME_WAIT src=127.0.0.1 dst=127.0.0.1 sport=51323 dport=3306 packets=10 bytes=682 src=127.0.0.1 dst=127.0.0.1 sport=3306 dport=51323 packets=8 bytes=1152 [ASSURED] mark=0 use=1
icmp     1 27 src=192.168.2.254 dst=192.168.2.44 type=8 code=0 id=63004 packets=149848 bytes=13786016 [UNREPLIED] src=192.168.2.44 dst=192.168.2.254 type=0 code=0 id=63004 packets=0 bytes=0 mark=0 use
```
</p>
</details>

```bash
"CG-NAT" - по сути тоже тип NAT overload.
Только имеет свои особенности, в частности:
- "EIM - (Endpoint Independent Mapping)" - позволяет для каждого сочетания частного ip адреса и порта клиента 
  гарантировать то же сочетание публичных ip адресов и портов.
  На некоторый период обеспечивает пользователя по сути статическим публичным ip - адресом и портом, 
  который можно использовать для внешних подключений (это важно для p2p, игр и мобильных устройств).  
- "EIF (Endpoint Independent Filtering)" -  отбрасывает пакеты, не предназначенные для внутренних адресов.
- "Hairpinning" -  обеспечивающей доступ одной машины к другой внутри сети по внешнему адресу.

Одним важным преимуществом CGNAT является то, что административно есть возможность 
ограничивать количество портов TCP и UDP, которые может использовать абонент. 
Это позволяет эффективно распределять порты среди пользователей, 
а также защищаться от DDoS-атак, 
использующих ботнет-сети и забивающих все свободные порты абонентов.
```