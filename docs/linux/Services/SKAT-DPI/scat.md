title: SKAT-DPI

# Шпаргалка по fastdpi


##  Посмотреть состояние пользователя по логину
   fdpi_ctrl list --policing --login <username>
```bash
   sd0.sdn.orionnet.ru:~ # fdpi_ctrl list --policing --login exhale
   Autodetected fastdpi params : dev='lo', port=29000
   connecting 127.0.0.1:29000 ...
  
   ================================
   exhale  HTB     dnlnk_rate=0.00mbit     dnlnk_ceil=0.00mbit       rrate=14719875(117.76mbit)  rburst=7359937(58.88mbit)       rceil=14719875(117.76mbit)      rcburst=7359937(58.88mbit)      
                        rate0=57.00mbit         ceil0=57.00mbit      
                        rate1=57.00mbit         ceil1=57.00mbit      
                        rate2=0.00mbit          ceil2=102.40mbit     
                        rate3=0.00mbit          ceil3=102.40mbit        
                        rate4=0.00mbit          ceil4=102.40mbit        
                        rate5=0.00mbit          ceil5=102.40mbit        
                        rate6=1000.00mbit       static                
                        rate7=0.00mbit          ceil7=1.02mbit       
                                                     HTB_INBOUND  rrate=14719875(117.76mbit)      rburst=7359937(58.88mbit)       rceil=14719875(117.76mbit)      rcburst=7359937(58.88mbit)      
                        rate0=57.00mbit         ceil0=102.40mbit        
                        rate1=57.00mbit         ceil1=102.40mbit    
                        rate2=0.00mbit          ceil2=102.40mbit        
                        rate3=0.00mbit          ceil3=102.40mbit        
                        rate4=0.00mbit          ceil4=102.40mbit        
                        rate5=0.00mbit          ceil5=102.40mbit        
                        rate6=1000.00mbit       static                
                        rate7=0.00mbit  ceil7=1.02mbit  
                        rate_100.0m
   ---------------------------------
   Result processing login=exhale :
   ---------------------------------
1/1/0
```

##  Посмотреть состояние пользователя по ip
    multibinding - объединение в одну полосу множество ip адресов

   fdpi_ctrl list --bind_multi --ip 172.29.10.3


## Посмотреть список ip адресов пользователя
   fdpi_ctrl list --bind_multi --login <username>

```bash
sd0.sdn.orionnet.ru:~ # fdpi_ctrl list --bind_multi --login exhale
Autodetected fastdpi params : dev='lo', port=29000
connecting 127.0.0.1:29000 ...

================================
exhale:172.16.53.92
exhale:172.17.0.1
exhale:172.17.0.93
---------------------------------
Result processing login=exhale :
------------------
1/3/0
```

## Посмотреть изменение профиля у абонента
  cat /opt/billing/log/quark.log | grep <username>

## Заблоченные абоненты

### Как выглядет заблоченный абон

```bash
    fdpi_ctrl list --policing --login myacc1
Autodetected fastdpi params : dev='lo', port=29000
connecting 127.0.0.1:29000 ...

================================
myacc1     HTB     dnlnk_rate=0.00mbit     dnlnk_ceil=0.00mbit rrate=125000(1.00mbit)      rburst=62500(0.50mbit)  rceil=125000(1.00mbit)  rcburst=62500(0.50mbit) 
                rate0=0.33mbit  ceil0=0.51mbit  
                rate1=0.33mbit  ceil1=0.51mbit  
                rate2=0.00mbit  ceil2=0.00mbit  
                rate3=0.33mbit  ceil3=1.00mbit  
                rate4=0.00mbit  ceil4=0.00mbit  
                rate5=0.00mbit  ceil5=0.00mbit  
                rate6=0.00mbit  ceil6=0.00mbit  
                rate7=0.00mbit  ceil7=1.02mbit  
                HTB_INBOUND     rrate=125000(1.00mbit)  rburst=62500(0.50mbit)       rceil=125000(1.00mbit)  rcburst=62500(0.50mbit) 
                rate0=0.33mbit  ceil0=0.51mbit  
                rate1=0.33mbit  ceil1=0.51mbit  
                rate2=0.00mbit  ceil2=0.00mbit  
                rate3=0.33mbit  ceil3=1.00mbit  
                rate4=0.00mbit  ceil4=0.00mbit  
                rate5=0.00mbit  ceil5=0.00mbit  
                rate6=0.00mbit  ceil6=0.00mbit  
                rate7=0.00mbit  ceil7=1.02mbit  
                blocked
---------------------------------
Result processing login=myacc1 :
----------------------------------
1/1/0
```

### Присутствие в настройках абона service=5 - это редирект на страницу блокировки

```bash
   fdpi_ctrl list --service 5 --login myacc1

Autodetected fastdpi params : dev='lo', port=29000
connecting 127.0.0.1:29000 ...

================================
myacc1     5       (0x10)  default
---------------------------------
Result processing login=myacc1 :
------------------
1/1/0
------------------
```

```bash
   fdpi_ctrl list --service 5 --ip 172.2.10.3

Autodetected fastdpi params : dev='lo', port=29000
connecting 127.0.0.1:29000 ...

================================
myacc1     5       (0x10)  default
---------------------------------
Result processing ip=172.2.10.3 :
------------------
1/1/0
------------------
```

### Список заблоченных абонов
   fdpi_ctrl list all --policing --profile.name blocked

## Количество пользователей по определенному диапазону
```bash
sd0.mnsk.sdn.orionnet.ru:~ # fdpi_ctrl list all --bind_multi | grep -c 172.29
176
--------------------------------------------------------------------------------------------------------
```


## Просмотр логов кварка
  tail -f /opt/billing/log/quark.log

## Просмотреть настроен ли CGNAT
fdpi_ctrl list --service 11 --ip 172.29.10.2

```bash
fdpi_ctrl list --service 11 --ip 172.29.10.2
Autodetected fastdpi params : dev='lo', port=29000
connecting 127.0.0.1:29000 ...

================================
172.29.10.2  11  (0x400)  mnsk_sd0_nat  { "nat_ip_pool" : "111.111.111.0/25", "nat_tcp_max_sessions" : 2000, "nat_udp_max_sessions" : 2000, "nat_type" : 0 }
---------------------------------
Result processing ip=172.29.10.2 : 
------------------
1/1/0
```
