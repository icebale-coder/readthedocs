title: cli JunOS

#Основы Juniper cli

## Навигация

### Режимы работы: 

#### operational mode - операционный режим.
```bash
В нем можно посмотреть текущую конфигурации, 
текущее состояние протоколов IGP, BGP и пр.
```

```bash
admin@MBR>  
```

#### configuration mode - конфигурационный режим.
```bash
В нем, как понятно из названия, настраивается конфигурация
```

```bash
admin@MBR>configure
admin@MBR# 

# либо через edit
admin@MBR>edit
admin@MBR# 
```

#### Команды просмотра/выбора из конфигурации
```bash
    !Матчинг по ключевым словам
    show configuration | display set | match "КЛЮЧЕВОЕ СЛОВО"
```

#### shell - режим шелла.
```bash
По сути в нем просто проваливаешься в JunOS, 
каковая являтся запиленным и протюниным FreeBSD.
В нем есть набор стандартных доступных команд Unix, тип top, uname и пр...
P.S. midnight commander мне туда поставить не удалось )
```

```bash
admin@MBR>start shell
admin@MBR> start shell 
% uname -a
JUNOS MBR 19.4R3.11 JUNOS 19.4R3.11 #0: 2020-10-08 21:58:24 UTC
builder@qnc-jre-emake1t.juniper.net:/volume/build/junos/19.4/
release/19.4R3.11/obj/powerpc/junos/bsd/kernels/JUNIPER-PPC/kernel  powerpc  
```

### Общая структура конфига 
    
Как правило конфиг состоит из следующих секций:

<details><summary>system {</summary>
<p>
```bash
    host-name MBR;
    root-authentication {
        encrypted-password /* SECRET-DATA */; ## SECRET-DATA
    }
    login {
        class read-only-local {
            permissions [ view view-configuration ];
            allow-commands "ping|traceroute";
        }
        user admin {
            uid 2000;
            class super-user;
            authentication {
                encrypted-password /* SECRET-DATA */; ## SECRET-DATA
            }
        }
    }
    services {
        ssh;
        telnet;
    }
    domain-name mydomain.ru;
    time-zone Asia/Bangkok;
    no-redirects;
    name-server {
        10.10.10.10;
        10.10.10.11;
    }
    syslog {
        user * {
            any emergency;
        }
        host 11.11.11.11 {
            any info;
            facility-override local7;
        }
        source-address lo.lo.lo.lo;
    }
    ntp {
        boot-server 12.12.12.12;
        server 12.12.12.12;
        source-address lo.lo.lo.lo;
    }
```
</p>
</details>
}
<details><summary>chassis { </summary>
<p>
```bash 
    aggregated-devices {
        ethernet {
            device-count 1;
        }
    }
    fpc 0 {
        pic 0 {
            framing lan;
        }
    }
    fpc 1 {
        pic 0 {
            framing lan;
        }
    }
```
</p>
</details>
}
<details><summary>security { </summary>
<p>

```bash     
    authentication-key-chains {
        key-chain isis-sec {
            key 1 {
                secret /* SECRET-DATA */; ## SECRET-DATA
                start-time "1999-12-31.23:00:00 +0700";
                algorithm md5;
                options basic;
            }
        }
    }
```
</p>
</details>
}
<details><summary>interfaces {</summary>
<p>

```bash 
        xe-0/0/0 {
            description "<< Ae0 10GE to N0 1/1 Po2 >>";
            framing {
                lan-phy;
            }
            gigether-options {
                802.3ad ae0;
            }
        }
        xe-0/0/1 {
            description "<< Ae0 10GE to N0 1/2 Po2 >>";
            framing {
                lan-phy;
            }
            gigether-options {
                802.3ad ae0;
            }
        }
        xe-0/0/2 {
            description "<< Ae0 10GE to N1 1/1 Po2 >>";
            framing {
                lan-phy;
            }
            gigether-options {
                802.3ad ae0;
            }
        }
        xe-0/0/3 {
            description "<< Ae0 10GE to N1 1/2 Po2 >>";
            framing {
                lan-phy;
            }
            gigether-options {
                802.3ad ae0;
            }
        }
        ae0 {
            description "<< 4x10GE to N0/1 Po2 >>";
            flexible-vlan-tagging;
            mtu 1522;
            encapsulation flexible-ethernet-services;
            aggregated-ether-options {
                lacp {
                    active;
                }
            }
            unit 16 {
                description "<< Desc16 >>";
                vlan-id 16;
                family inet {
                    address 16.16.16.3/30;
                }
            }
            unit 100 {
                description "<< Desc100 >>";
                vlan-id 100;
                family inet {
                    address 100.100.100.3/30;
                }
            }
            unit 200 {
                description "<< Desc200 >>";
                vlan-id 200;
                family inet {
                    address 200.200.200.3/30;
                }
            }
            unit 201 {
               description "<< Desc201 >>";
                vlan-id 201;
                family inet {
                    address 201.201.201.3/30;
                }
            }
            unit 900 {
                description "<< Desc900 >>";
                vlan-id 900;
                family inet {
                    address 16.16.16.3/30;
                }
            }
        }

        fxp0 {
            unit 0 {
                family inet {
                    address m.g.m.t/24;
                }
            }
        }
        lo0 {
            description BGP;
            unit 0 {
                family inet {
                    filter {
                        input ACL;
                    }
                    address lo.lo.lo.lo/32;
                }
            }
        }
    }
```
</p>
</details>
}    
<details><summary>snmp {</summary>
<p>

```bash 
    location DefaultCity;
    community my_comm {
        authorization read-only;
        clients {
            14.14.14.14/32;
        }
    }
```
</p>
</details>
}    
<details><summary>forwarding-options {</summary>
<p>

```bash 
    sampling {
        sample-once;
        input {
            rate 10;
            run-length 0;
        }
        family inet {
            output {
                flow-server 15.15.15.15 {
                    port 9996;
                    source-address lo.lo.lo.lo;
                    version 5;
                }
            }
        }
    }
```
</p>
</details>
}
<details><summary>policy-options {</summary>
<p>

```bash 

    prefix-list AS11111 {
        1.0.0.0/23;
        2.0.0.0/24;
    }
    policy-statement BOGONS {
        term rfc6890 {
            from {
                route-filter 10.0.0.0/8 orlonger accept;
                route-filter 172.16.0.0/12 orlonger accept;
                route-filter 192.168.0.0/16 orlonger accept;
            }
        }
        term default {
            then reject;
        }
    }
    policy-statement REJECT_ALL {
        then reject;
    }    
    policy-statement PS-1 {
        term AS11111 {
            from {
                route-filter 111.111.111.0/24 exact;
            }
            then accept;
        }
    }    
```
</p>
</details>
}
<details><summary>firewall {</summary>
<p>

```bash 
        filter ACL {
            term terminal-access {
                from {
                    source-address {
                        /* OoBM network */
                        20.20.20.0/24;
                        /* Office GW */
                        30.30.30.30/32;
                    }
                    protocol tcp;
                    port [ ssh telnet ];
                }
                then accept;
            }
            term terminal-access-default {
                from {
                    protocol tcp;
                    port [ ssh telnet ];
                }
                then {
                    discard;
                }
            }
            term trusted {
                from {
                    source-prefix-list {
                        TRUSTED_NETWORK;
                    }
                }
                then accept;
            }
            term icmp {
                from {
                    protocol icmp;
                }
                then {
                    policer ICMP_POLICER;
                    count ICMP_POLICER;
                    accept;
                }
            }
            term snmp {
                from {
                    source-address {
                        40.40.40.40/32;
                    }
                    protocol udp;
                    port snmp;
                }
                then accept;
            }
            term default {
                then {
                    discard;
                }
            }
        }
        filter SAMPLE {
            interface-specific;
            term default {
                then {
                    sample;
                    accept;
                }
            }
        }
    }
```
</p>
</details>
}
<details><summary>routing-options {</summary>
<p>

```bash 
    static {
        route 0.0.0.0/0 {
            qualified-next-hop 50.50.50.50 {
                preference 100;
            }
            qualified-next-hop 60.60.60.60 {
                preference 90;
            }
        }
        /* <static example */
        route 50.50.50.0/25 next-hop 11.11.11.11;
    router-id lo.lo.lo.lo;
    autonomous-system 11111;
    aggregate {
        route 1.0.0.0/19 passive;
        route 2.2.2.0/26 passive;
    }
```
</p>
</details>
}
<details><summary>protocols {</summary>
<p>

```bash 
    ospf {
        area 0.0.0.0 {
            interface lo0.0;
            interface ae0.16;
            interface ae0.900;
        }
    }
    bgp {
        group EXTERNAL_PEER {
            type external;
            passive;
            import [ PS-1 PS-2 ];
            family inet {
                unicast {
                    prefix-limit {
                        maximum 1000000;
                        teardown 95;
                    }
                }
            }
            export [ PS-3 REJECT_ALL ];
            neighbor 100.100.100.1 {
                description "== EXTERNAL NEIGHBOR 1 ==";
                local-address 100.100.100.2 ;
                peer-as 50427;
        group INTERNAL_PEER {
            type internal;
            family inet {
                unicast {
                    prefix-limit {
                        maximum 2000;
                        teardown 90;
                    }
                }
            }
            export [ PS-4 PS-5 REJECT_ALL ];
            neighbor 200.200.200.1 {
                description "== INTERNAL NEIGHBOR 1 ==";
                local-address 200.200.200.2;
                import [ PS-6 REJECT_ALL ];
            }
            neighbor 201.201.201.1 {
                description "== INTERNAL NEIGHBOR 2 ==";
                local-address 201.201.201.2;
                import [ PS-7 REJECT_ALL ];
            }
        }
    }
    lldp {
        interface xe-0/0/0;
        interface xe-0/0/1;
        interface xe-0/0/2;
        interface xe-0/0/3;
    }
```
</p>
</details>
}


Каждая секция несет свой функционал.
Например в секции "interfaces" как не трудно догадаться из названия перечисляются интерфейсы как физические так и логические. 

!!! note "Заметка"
	У джуна портчаны называются aggregated ethernet, например ae0

### Навигация по конфигу 
    ВСЁ ОЧЕНЬ УДОБНО!!! )

```bash
# из operational mode
show configuration <секция_в_конфиге подсекция_в_конфиге 
под_подсекция_в_конфиге и т.д...> 

# пример показа lo0
admin@MBR> show configuration interfaces lo0 
description BGP;
unit 0 {
    family inet {
        filter {
            input ACL;
        }
        address lo.lo.lo.lo/32;
    }
}
```

```bash
# из configuration mode
show <секция_в_конфиге подсекция_в_конфиге под_подсекция_в_конфиге
и т.д...> 

# пример показа lo0
admin@MBR>edit
admin@MBR# show interfaces lo0 
description BGP;
unit 0 {
    family inet {
        filter {
            input ACL;
        }
        address lo.lo.lo.lo/32;
    }
}

```

### Редактирование конфига 

    Я для себя определил 2 удобных режима

#### 1. load merge/update terminal (relative)
     Загружает конфиг из терминала в нужную секцию

!!! note "relative"    
    Параметр "relative" нужен для того, чтобы указывать, что вносимые изменения относятся к текущему контексту редактирования. Без параметра "relative" Джун будет считать что постится конфиг от корня конфигурации


#### 1.1. load merge terminal relative 
      Добавляет конфигурацию путем слияния существущей и новой
      Постим в нужном месте кусок своей конфигурации

#### 1.2. load update terminal relative
      Добавляет конфигурацию путем удаления существущей и добавлением новой
      Постим в нужном месте кусок своей конфигурации

#### 2. set/delete 
      Перемещаемся в нужное место конфига, 
      вносим/удаляем новые параметры через команду "set/delete"


#### Примеры

!!! note "Пример использования load merge terminal relative"

<details><summary>Необходимо создать новый сабинтерфейс 900 в ae0</summary>
<p>
    

* Заходим в configure mode и перемещаемся в секцию interfaces ae0

```bash
    admin@MBR>edit
    admin@MBR#edit interface ae0 

    [edit interfaces ae0]
```

* Добавляем через терминал недостающую секцию конфига


```bash
Примечание: для того, чтобы завершить ввод в терминале необходимо ввести комбинацию Ctrl-D
```

```bash
admin@MBR# load merge terminal relative 
[Type ^D at a new line to end input]
    unit 900 {
        description "<< Desc900 >>";
        vlan-id 900;
        family inet {
            address 16.16.16.3/30;
        }
    }
load complete

[edit interfaces ae0]
```

* Перемещаемся в корень конфига
```bash
admin@MBR# top 
[edit ]
```
* Проверяем что поменяется в конфиге
```bash
    admin@MBR# show | compare 
    [edit interfaces ae0]
+    unit 900 {
+        description "<< Desc900 >>";
+        vlan-id 900;
+        family inet {
+            address 16.16.16.3/30;
+        }
+    }
```

* Применяем изменения и выходим  в operation mode
```bash
    admin@MBR# commit
    commit complete

    [edit]
    admin@MBR# quit 
    Exiting configuration mode

    admin@MBR0> 
```
</p>
</details>

```bash
```

!!! note "Пример использования set/delete "
<details><summary>Необходимо поменять ip адрес в ae0.900</summary>
<p>
    
* Заходим в configure mode и перемещаемся в секцию interfaces ae0

```bash
    admin@MBR>edit
    admin@MBR#edit interface ae0 unit900 family inet

```

* Удаляем старый ip адрес 
```bash
    admin@MBR#delete address 16.16.16.3/30
```

* Добавляем новый ip адрес 
```bash
    admin@MBR#set address 17.17.17.3/30
```

* Проверяем что поменяется в конфиге
```bash
    admin@MBR# show | compare 
    [edit interfaces ae0 unit 900 family inet]
-      address 16.16.16.3/30;
+      address 17.17.17.3/30;
```

* Применяем изменения и выходим  в operation mode
```bash
    admin@MBR# commit
    commit complete

    [edit]
    admin@MBR# quit 
    Exiting configuration mode

    admin@MBR0> 
```

</p>
</details>

### Commit/Rollback

#### show | compare 
```bash 
"Просмотр изменений до комитта"
    admin@MBR# show | compare 
    [edit interfaces ae0 unit 900 family inet]
-      address 16.16.16.3/30;
+      address 17.17.17.3/30;
```

#### show | compare rollback 1
```bash
"Посмотреть незакоммиченные данные на одну итерацию до текущего коммита"
show | compare rollback 1
```
#### Отмена незакоммченных изменений 
```bash
rollback 0
```

#### rollback 1 
!!! note "Важно!"
    При откате к предыдущему коммиту небходимо делать после этого commit - обязателено!
    По сути "rollback" - загружает конфиг, но не применяет его. Как раз применяет конфиг команда "commit"

```bash
"Откатить конфиг к предыдущему состоянию"
rollback 1

"для применения данного rolback-а нужно сделать 'commit'"
commit
```

#### rollback 2
```bash
"Откат изменений на 2 итерации назад"
rollback 2
```

#### commit check
```bash
"Проверка валидности конфига перед коммитом"
commit check
```

#### commit
```bash
"Применить изменения"
commit
```

<details><summary>commit | display detail</summary>
<p>

```bash
"Можно посмотреть в деталях,"
"что происходит под капотом внутри коробки при применения commit"

"commit | display detail"
2022-02-01 12:07:21.222603 +03: Obtaining lock for commit
2022-02-01 12:07:21.259285 +03: merging latest committed configuration
2022-02-01 12:07:21.259561 +03: Using fast-diff method to generate diff
2022-02-01 12:07:21.427182 +03: UI extensions feature is not configured
2022-02-01 12:07:21.427404 +03: Started running translation script
2022-02-01 12:07:21.485967 +03: No delta input for translation
2022-02-01 12:07:21.486049 +03: Finished running translation script
2022-02-01 12:07:21.486219 +03: start loading commit script changes
2022-02-01 12:07:21.486288 +03: no commit script changes
2022-02-01 12:07:21.486354 +03: no transient commit script changes
2022-02-01 12:07:21.486411 +03: finished loading commit script changes
2022-02-01 12:07:21.486454 +03: No translation output from the scripts
2022-02-01 12:07:21.486484 +03: Preparing Fast-diff post translation load
2022-02-01 12:07:21.486926 +03: building groups inheritance path proportional in candidate db
2022-02-01 12:07:21.487157 +03: finished groups inheritance path
2022-02-01 12:07:21.487196 +03: copying juniper.db to juniper.data+
2022-02-01 12:07:21.534980 +03: finished copying juniper.db to juniper.data+
2022-02-01 12:07:21.535143 +03: exporting juniper.conf
2022-02-01 12:07:21.686577 +03: expanding interface-ranges
2022-02-01 12:07:21.686657 +03: finished expanding interface-ranges
2022-02-01 12:07:21.686699 +03: setup foreign files
2022-02-01 12:07:21.695674 +03: propagating foreign files
2022-02-01 12:07:21.942159 +03: constraints passed in mustd - not checking constraints in propagation
2022-02-01 12:07:21.951389 +03: complete foreign files
2022-02-01 12:07:21.996461 +03: dropping unchanged foreign files
2022-02-01 12:07:21.996584 +03: start ffp propagate
2022-02-01 12:07:22.19420 +03: executing 'ffp propagate'
2022-02-01 12:07:22.20634 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:22.22631 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:22.35445 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:22.46359 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:22.71007 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:22.71520 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:22.82624 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:22.83942 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:22.84508 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:22.85134 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:22.96771 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:22.97302 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:22.108417 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:22.119233 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:22.148499 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:22.149076 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:22.160414 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:22.161886 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:22.162486 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:22.163086 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:22.165586 +03: finish ffp propagate
2022-02-01 12:07:22.165842 +03: daemons checking new configuration
2022-02-01 12:07:22.166104 +03: Spawning Routing protocols process to check new configuration
2022-02-01 12:07:22.167794 +03: Spawning Chassis control process to check new configuration
2022-02-01 12:07:22.169679 +03: sending commit-check command to Firewall process(26916)
2022-02-01 12:07:22.170700 +03: sending commit-check command to Interface control process(26911)
2022-02-01 12:07:22.173243 +03: Collecting status of Interface control process
2022-02-01 12:07:22.388247 +03: Spawning Simple Network Management Protocol process to check new configuration
2022-02-01 12:07:22.413153 +03: Collecting status of Simple Network Management Protocol process
2022-02-01 12:07:22.413430 +03: Spawning Management Information Base II process to check new configuration
2022-02-01 12:07:22.432746 +03: Collecting status of Chassis control process
2022-02-01 12:07:22.436716 +03: Spawning SONET Automatic Protection Switching process to check new configuration
2022-02-01 12:07:22.441093 +03: Collecting status of Management Information Base II process
2022-02-01 12:07:22.441366 +03: Spawning Virtual Router Redundancy Protocol process to check new configuration
2022-02-01 12:07:22.454532 +03: Collecting status of SONET Automatic Protection Switching process
2022-02-01 12:07:22.455788 +03: Spawning Packet Forwarding Engine statistics management process to check new configuration
2022-02-01 12:07:22.474754 +03: Collecting status of Virtual Router Redundancy Protocol process
2022-02-01 12:07:22.475997 +03: Spawning Traffic sampling control process to check new configuration
2022-02-01 12:07:22.497279 +03: Collecting status of Packet Forwarding Engine statistics management process
2022-02-01 12:07:22.498371 +03: Spawning Remote operations process to check new configuration
2022-02-01 12:07:22.505934 +03: Collecting status of Traffic sampling control process
2022-02-01 12:07:22.507738 +03: Spawning Redundancy interface management process to check new configuration
2022-02-01 12:07:22.525476 +03: Collecting status of Redundancy interface management process
2022-02-01 12:07:22.526949 +03: Spawning Dynamic flow capture service to check new configuration
2022-02-01 12:07:22.533345 +03: Collecting status of Remote operations process
2022-02-01 12:07:22.534597 +03: Spawning Enhanced Session Management process to check new configuration
2022-02-01 12:07:22.549692 +03: Collecting status of Dynamic flow capture service
2022-02-01 12:07:22.550145 +03: Spawning Subscriber management process to check new configuration
2022-02-01 12:07:22.586900 +03: Collecting status of Subscriber management process
2022-02-01 12:07:22.588087 +03: Spawning Ethernet Clock Synchronization Process to check new configuration
2022-02-01 12:07:22.669661 +03: Collecting status of Ethernet Clock Synchronization Process
2022-02-01 12:07:22.670579 +03: Spawning App-engine virtual machine management process to check new configuration
2022-02-01 12:07:22.698832 +03: Collecting status of App-engine virtual machine management process
2022-02-01 12:07:22.699460 +03: Spawning Kernel offload daemon to check new configuration
2022-02-01 12:07:22.706548 +03: Collecting status of Routing protocols process
2022-02-01 12:07:22.708652 +03: Spawning SCC Chassisd Process to check new configuration
2022-02-01 12:07:22.714793 +03: Collecting status of Enhanced Session Management process
2022-02-01 12:07:22.716314 +03: Spawning Logical system multiplexer process to check new configuration
2022-02-01 12:07:22.722478 +03: Collecting status of Kernel offload daemon
2022-02-01 12:07:22.723654 +03: Spawning Layer 2 address flooding and learning process to check new configuration
2022-02-01 12:07:22.754311 +03: Collecting status of Logical system multiplexer process
2022-02-01 12:07:22.755496 +03: Spawning Layer 2 Control Protocol process to check new configuration
2022-02-01 12:07:22.784293 +03: Collecting status of SCC Chassisd Process
2022-02-01 12:07:22.790784 +03: Collecting status of Layer 2 address flooding and learning process
2022-02-01 12:07:22.856217 +03: Collecting status of Firewall process
2022-02-01 12:07:22.869230 +03: Collecting status of Layer 2 Control Protocol process
2022-02-01 12:07:22.907787 +03: updating shared candidate configuration
2022-02-01 12:07:22.909063 +03: Rebuilding groups inheritance path proportional in candidate db
2022-02-01 12:07:22.909263 +03: Finished rebuilding groups inheritance pathin candidate db
2022-02-01 12:07:22.909452 +03: updating commit revision
2022-02-01 12:07:23.119573 +03: obtaining db lock on  re1
2022-02-01 12:07:23.420647 +03: re-revision: re0-1648708549-1851, other-re-revision: re0-1648708549-1851(0)
2022-02-01 12:07:23.420802 +03: UI extensions feature is not configured
configuration check succeeds
2022-02-01 12:07:23.445773 +03: executing 'ffp synchronize'
2022-02-01 12:07:23.447090 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:23.447837 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:23.459064 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:23.470237 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:23.496468 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:23.497260 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:23.508544 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:23.509891 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:23.510468 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:23.511015 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:23.511983 +03: copying file '//var/etc/stp_context.id+' to 're1'
2022-02-01 12:07:23.637757 +03: copying file '//var/etc/stp_all_port.id+' to 're1'
2022-02-01 12:07:23.761755 +03: copying file '//var/etc/non_stp_port.id+' to 're1'
2022-02-01 12:07:23.989715 +03: copying file '//var/etc/mobile_addr_assignment.id+' to 're1'
2022-02-01 12:07:24.115447 +03: copying file '//var/etc/if_alias_map+' to 're1'
2022-02-01 12:07:24.239696 +03: copying file '//var/etc/stp_port.id+' to 're1'
2022-02-01 12:07:24.364347 +03: copying file '//var/etc/stp_ae_port.id+' to 're1'
2022-02-01 12:07:24.488661 +03: copying file '//var/etc/stp_port.id+' to 're1'
2022-02-01 12:07:24.614933 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:24.615556 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:24.627187 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:24.638753 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:24.664662 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:24.665193 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:24.676699 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:24.678077 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:24.678669 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:24.679357 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:24.681603 +03: sending pull-configuration rpc to re1
2022-02-01 12:07:24.681711 +03: filename /var/run/db/private/juniper-99260.patch, size 287
2022-02-01 12:07:24.930650 +03: pull-configuration success. URL: /var/tmp/juniper-99260.patch
2022-02-01 12:07:24.930740 +03: sending load-patch rpc to re1
2022-02-01 12:07:25.30605 +03: remote load-configuration success on re1
2022-02-01 12:07:25.30665 +03: sending file-delete rpc to re1
2022-02-01 12:07:25.134623 +03: syncing commit db revision to  re1 
2022-02-01 12:07:25.134687 +03: asking re1 to commit
re1: 
2022-02-01 12:07:25.611523 +03: Obtaining lock for commit
2022-02-01 12:07:25.612632 +03: updating commit revision
2022-02-01 12:07:25.612908 +03: Started running translation script
2022-02-01 12:07:25.612949 +03: Finished running translation script
2022-02-01 12:07:25.613057 +03: start loading commit script changes
2022-02-01 12:07:25.613107 +03: no commit script changes
2022-02-01 12:07:25.613149 +03: no transient commit script changes
2022-02-01 12:07:25.613210 +03: finished loading commit script changes
2022-02-01 12:07:25.613580 +03: building groups inheritance path proportional in candidate db
2022-02-01 12:07:25.613795 +03: finished groups inheritance path
2022-02-01 12:07:25.613866 +03: copying juniper.db to juniper.data+
2022-02-01 12:07:25.664659 +03: finished copying juniper.db to juniper.data+
2022-02-01 12:07:25.664810 +03: exporting juniper.conf
2022-02-01 12:07:25.811948 +03: expanding interface-ranges
2022-02-01 12:07:25.812017 +03: finished expanding interface-ranges
2022-02-01 12:07:25.812093 +03: setup foreign files
2022-02-01 12:07:25.817678 +03: propagating foreign files
2022-02-01 12:07:26.60812 +03: constraints passed in mustd - not checking constraints in propagation
2022-02-01 12:07:26.69655 +03: complete foreign files
2022-02-01 12:07:26.119194 +03: dropping unchanged foreign files
2022-02-01 12:07:26.119293 +03: start ffp propagate
2022-02-01 12:07:26.141125 +03: executing 'ffp propagate'
2022-02-01 12:07:26.142303 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:26.144306 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:26.157144 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:26.167934 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:26.192585 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:26.193085 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:26.204476 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:26.205816 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:26.206386 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:26.207053 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:26.214181 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:26.214679 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:26.225840 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:26.237640 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:26.270817 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:26.271569 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:26.283700 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:26.285265 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:26.285842 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:26.286458 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:26.288804 +03: finish ffp propagate
2022-02-01 12:07:26.288956 +03: daemons checking new configuration
2022-02-01 12:07:26.289145 +03: Spawning Routing protocols process to check new configuration
2022-02-01 12:07:26.290297 +03: Spawning Chassis control process to check new configuration
2022-02-01 12:07:26.292071 +03: sending commit-check command to Firewall process(26836)
2022-02-01 12:07:26.292673 +03: sending commit-check command to Interface control process(26826)
2022-02-01 12:07:26.293940 +03: Collecting status of Interface control process
2022-02-01 12:07:26.467771 +03: Spawning Simple Network Management Protocol process to check new configuration
2022-02-01 12:07:26.469241 +03: Collecting status of Chassis control process
2022-02-01 12:07:26.469534 +03: Spawning Management Information Base II process to check new configuration
2022-02-01 12:07:26.489617 +03: Collecting status of Simple Network Management Protocol process
2022-02-01 12:07:26.489969 +03: Spawning Virtual Router Redundancy Protocol process to check new configuration
2022-02-01 12:07:26.494335 +03: Collecting status of Management Information Base II process
2022-02-01 12:07:26.494755 +03: Spawning Packet Forwarding Engine statistics management process to check new configuration
2022-02-01 12:07:26.515297 +03: Collecting status of Virtual Router Redundancy Protocol process
2022-02-01 12:07:26.515701 +03: Spawning Enhanced Session Management process to check new configuration
2022-02-01 12:07:26.522591 +03: Collecting status of Packet Forwarding Engine statistics management process
2022-02-01 12:07:26.522896 +03: Spawning Subscriber management process to check new configuration
2022-02-01 12:07:26.545184 +03: Collecting status of Subscriber management process
2022-02-01 12:07:26.545392 +03: Spawning Ethernet Clock Synchronization Process to check new configuration
2022-02-01 12:07:26.589533 +03: Collecting status of Ethernet Clock Synchronization Process
2022-02-01 12:07:26.589984 +03: Spawning App-engine virtual machine management process to check new configuration
2022-02-01 12:07:26.607531 +03: Collecting status of App-engine virtual machine management process
2022-02-01 12:07:26.607902 +03: Spawning Logical system multiplexer process to check new configuration
2022-02-01 12:07:26.626891 +03: Collecting status of Logical system multiplexer process
2022-02-01 12:07:26.627398 +03: Spawning Layer 2 address flooding and learning process to check new configuration
2022-02-01 12:07:26.650501 +03: Collecting status of Enhanced Session Management process
2022-02-01 12:07:26.651229 +03: Spawning Layer 2 Control Protocol process to check new configuration
2022-02-01 12:07:26.666423 +03: Collecting status of Layer 2 address flooding and learning process
2022-02-01 12:07:26.689451 +03: Collecting status of Routing protocols process
2022-02-01 12:07:26.713803 +03: Collecting status of Layer 2 Control Protocol process
2022-02-01 12:07:27.419038 +03: Collecting status of Firewall process
2022-02-01 12:07:27.419190 +03: commit wrapup...
2022-02-01 12:07:27.419319 +03: activating '/var/etc/ntp.conf'
2022-02-01 12:07:27.419616 +03: start ffp activate
2022-02-01 12:07:27.441427 +03: executing 'ffp activate'
2022-02-01 12:07:27.442566 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:27.443295 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:27.454189 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:27.464992 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:27.489481 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:27.489977 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:27.501009 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:27.502335 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:27.502890 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:27.503512 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:27.505024 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:27.505517 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:27.516314 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:27.526991 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:27.556495 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:27.557104 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:27.568247 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:27.591703 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:27.592343 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:27.592946 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:27.594795 +03: activating '/var/etc/keyadmin.conf'
2022-02-01 12:07:27.598389 +03: activating '/var/etc/pam.conf'
2022-02-01 12:07:27.598594 +03: activating '/var/etc/pam_radius.conf'
2022-02-01 12:07:27.598686 +03: activating '/var/etc/pam_tacplus.conf'
2022-02-01 12:07:27.598784 +03: activating '/var/etc/ifinfo.conf'
2022-02-01 12:07:27.598915 +03: activating '/var/etc/issue'
2022-02-01 12:07:27.599017 +03: activating '/var/etc/certs'
2022-02-01 12:07:27.603266 +03: activating '/var/etc/motd'
2022-02-01 12:07:27.605788 +03: activating '/var/etc/ifmdata.conf'
2022-02-01 12:07:27.605996 +03: activating '/var/etc/max-db-size-cfg'
2022-02-01 12:07:27.606162 +03: activating '/var/etc/subs-mgmt-cfg'
2022-02-01 12:07:27.606271 +03: activating '/var/etc/vmm.conf'
2022-02-01 12:07:27.606353 +03: activating '/var/etc/db_ext-cfg'
2022-02-01 12:07:27.606487 +03: activating '/var/etc/ephinst.conf'
2022-02-01 12:07:27.606611 +03: activating '/var/etc/motd.junos'
2022-02-01 12:07:27.606725 +03: executing foreign_commands
2022-02-01 12:07:27.606777 +03: /bin/sh /etc/rc.ui ui_setup_users (sh)
2022-02-01 12:07:27.614381 +03: not executing ui_commit in rc.ui
2022-02-01 12:07:27.624632 +03: finish ffp activate
2022-02-01 12:07:27.624719 +03: db_groups_info_clear start
2022-02-01 12:07:27.629978 +03: db_groups_info_clear done
2022-02-01 12:07:27.630023 +03: activating '/var/run/db/juniper.data'
2022-02-01 12:07:27.650455 +03: Rotate backup configs
2022-02-01 12:07:27.917023 +03: ssync begins
2022-02-01 12:07:27.925396 +03: ssync ends
2022-02-01 12:07:27.925443 +03: ssync begins
2022-02-01 12:07:27.978638 +03: ssync ends
2022-02-01 12:07:27.978695 +03: notifying daemons of new configuration
2022-02-01 12:07:27.978765 +03:  notifying none(0)
2022-02-01 12:07:27.978820 +03: cannot signal 'None process', signal 1, no pid
2022-02-01 12:07:27.978901 +03:  notifying rpd(5)
2022-02-01 12:07:27.978989 +03: signaling 'Routing protocols process', pid 26810, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.979080 +03:  notifying chassisd(6)
2022-02-01 12:07:27.979208 +03: signaling 'Chassis control process', pid 21014, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.979312 +03:  notifying xntpd(7)
2022-02-01 12:07:27.979358 +03: cannot signal 'Network time process', signal 1, no pid
2022-02-01 12:07:27.979414 +03:  notifying dfwd(12)
2022-02-01 12:07:27.979501 +03: signaling 'Firewall process', pid 26836, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.979558 +03:  notifying dcd(13)
2022-02-01 12:07:27.981096 +03: signaling 'Interface control process', pid 26826, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.981193 +03:  notifying snmpd(15)
2022-02-01 12:07:27.991619 +03: signaling 'Simple Network Management Protocol process', pid 26827, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.991981 +03:  notifying mib2d(17)
2022-02-01 12:07:27.993823 +03: signaling 'Management Information Base II process', pid 63928, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.994222 +03:  notifying vrrpd(19)
2022-02-01 12:07:27.996670 +03: signaling 'Virtual Router Redundancy Protocol process', pid 21036, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:27.997020 +03:  notifying pfed(22)
2022-02-01 12:07:28.2266 +03: signaling 'Packet Forwarding Engine statistics management process', pid 21613, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.2732 +03:  notifying bbe-smgd(63)
2022-02-01 12:07:28.3218 +03: signaling 'Enhanced Session Management process', pid 21078, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.4205 +03:  notifying smid(77)
2022-02-01 12:07:28.6783 +03: signaling 'Subscriber management process', pid 21053, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.6926 +03:  notifying transportd(81)
2022-02-01 12:07:28.7400 +03: signaling 'Transport control process', pid 21057, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.7709 +03:  notifying clksyncd(83)
2022-02-01 12:07:28.8790 +03: signaling 'Ethernet Clock Synchronization Process', pid 21049, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.9189 +03:  notifying sdk-vmmd(88)
2022-02-01 12:07:28.9529 +03: signaling 'App-engine virtual machine management process', pid 21017, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:28.9971 +03:  notifying lrmuxd(116)
2022-02-01 12:07:28.10294 +03: cannot signal 'Logical system multiplexer process', signal 1, no pid
2022-02-01 12:07:28.10644 +03:  notifying l2ald(121)
2022-02-01 12:07:28.10807 +03: cannot signal 'Layer 2 address flooding and learning process', signal 1, no pid
2022-02-01 12:07:28.10916 +03:  notifying l2cpd(123)
2022-02-01 12:07:28.10986 +03: cannot signal 'Layer 2 Control Protocol process', signal 1, no pid
2022-02-01 12:07:28.16477 +03: ssync begins
2022-02-01 12:07:28.25028 +03: ssync ends
2022-02-01 12:07:28.25223 +03: commit complete
commit complete
2022-02-01 12:07:27.637098 +03: commit wrapup...
2022-02-01 12:07:27.637245 +03: activating '/var/etc/ntp.conf'
2022-02-01 12:07:27.637759 +03: start ffp activate
2022-02-01 12:07:27.662025 +03: executing 'ffp activate'
2022-02-01 12:07:27.663222 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:27.664098 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:27.675982 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:27.687159 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:27.712996 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:27.713541 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:27.724918 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:27.726242 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:27.726856 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:27.727506 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:27.729012 +03: Processing shared library: libcommon-filetab-ffp.so
2022-02-01 12:07:27.729802 +03: Processing shared library: libcosd-ffp.so
2022-02-01 12:07:27.742460 +03: Processing shared library: libdfwd-ffp.so
2022-02-01 12:07:27.753963 +03: Processing shared library: libdprofdb-ffp.so
2022-02-01 12:07:27.785472 +03: Processing shared library: libmaster-password-ffp.so
2022-02-01 12:07:27.786306 +03: Processing shared library: liboiu-ffp.so
2022-02-01 12:07:27.798910 +03: Processing shared library: librrdl-ffp.so
2022-02-01 12:07:27.823653 +03: Processing shared library: libvsyncd-ffp.so
2022-02-01 12:07:27.824373 +03: Processing shared library: libntf-agent-ffp.so
2022-02-01 12:07:27.825006 +03: Processing shared library: libmtx-filetab-ffp.so
2022-02-01 12:07:27.827321 +03: activating '/var/etc/keyadmin.conf'
2022-02-01 12:07:27.832174 +03: activating '/var/etc/pam.conf'
2022-02-01 12:07:27.832376 +03: activating '/var/etc/pam_radius.conf'
2022-02-01 12:07:27.832480 +03: activating '/var/etc/pam_tacplus.conf'
2022-02-01 12:07:27.832564 +03: activating '/var/etc/ifinfo.conf'
2022-02-01 12:07:27.832665 +03: activating '/var/etc/issue'
2022-02-01 12:07:27.832756 +03: activating '/var/etc/certs'
2022-02-01 12:07:27.843814 +03: activating '/var/etc/motd'
2022-02-01 12:07:27.845460 +03: activating '/var/etc/ifmdata.conf'
2022-02-01 12:07:27.845556 +03: activating '/var/etc/max-db-size-cfg'
2022-02-01 12:07:27.845667 +03: activating '/var/etc/subs-mgmt-cfg'
2022-02-01 12:07:27.845790 +03: activating '/var/etc/vmm.conf'
2022-02-01 12:07:27.845879 +03: activating '/var/etc/db_ext-cfg'
2022-02-01 12:07:27.846005 +03: activating '/var/etc/ephinst.conf'
2022-02-01 12:07:27.846157 +03: activating '/var/etc/motd.junos'
2022-02-01 12:07:27.846269 +03: executing foreign_commands
2022-02-01 12:07:27.846500 +03: /bin/sh /etc/rc.ui ui_setup_users (sh)
2022-02-01 12:07:27.866667 +03: not executing ui_commit in rc.ui
2022-02-01 12:07:27.878155 +03: finish ffp activate
2022-02-01 12:07:27.878239 +03: Using show | compare method to generate diff
2022-02-01 12:07:29.117585 +03: updating LR database with master changes
2022-02-01 12:07:29.136093 +03: db_groups_info_clear start
2022-02-01 12:07:29.139479 +03: db_groups_info_clear done
2022-02-01 12:07:29.139524 +03: activating '/var/run/db/juniper.data'
2022-02-01 12:07:29.160687 +03: Rotate backup configs
2022-02-01 12:07:29.484168 +03: ssync begins
2022-02-01 12:07:29.492755 +03: ssync ends
2022-02-01 12:07:29.492845 +03: ssync begins
2022-02-01 12:07:29.515164 +03: ssync ends
2022-02-01 12:07:29.515230 +03: notifying daemons of new configuration
2022-02-01 12:07:29.515362 +03:  notifying none(0)
2022-02-01 12:07:29.515422 +03: cannot signal 'None process', signal 1, no pid
2022-02-01 12:07:29.515548 +03:  notifying rpd(5)
2022-02-01 12:07:29.515759 +03: signaling 'Routing protocols process', pid 26866, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.515847 +03:  notifying chassisd(6)
2022-02-01 12:07:29.516061 +03: signaling 'Chassis control process', pid 21029, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.516161 +03:  notifying xntpd(7)
2022-02-01 12:07:29.516679 +03: signaling 'Network time process', pid 26865, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.516941 +03:  notifying dfwd(12)
2022-02-01 12:07:29.517429 +03: signaling 'Firewall process', pid 26916, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.517660 +03:  notifying dcd(13)
2022-02-01 12:07:29.519282 +03: signaling 'Interface control process', pid 26911, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.519618 +03:  notifying snmpd(15)
2022-02-01 12:07:29.530217 +03: signaling 'Simple Network Management Protocol process', pid 26914, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.531304 +03:  notifying mib2d(17)
2022-02-01 12:07:29.531583 +03: signaling 'Management Information Base II process', pid 26913, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.531895 +03:  notifying apsd(18)
2022-02-01 12:07:29.533582 +03: signaling 'SONET Automatic Protection Switching process', pid 26868, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.534154 +03:  notifying vrrpd(19)
2022-02-01 12:07:29.535263 +03: signaling 'Virtual Router Redundancy Protocol process', pid 21056, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.536129 +03:  notifying pfed(22)
2022-02-01 12:07:29.545177 +03: signaling 'Packet Forwarding Engine statistics management process', pid 26915, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.546028 +03:  notifying sampled(25)
2022-02-01 12:07:29.554020 +03: signaling 'Traffic sampling control process', pid 26869, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.554603 +03:  notifying rmopd(30)
2022-02-01 12:07:29.562895 +03: signaling 'Remote operations process', pid 26870, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.563790 +03:  notifying rdd(44)
2022-02-01 12:07:29.566856 +03: signaling 'Redundancy interface management process', pid 26877, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.567551 +03:  notifying dfcd(46)
2022-02-01 12:07:29.568837 +03: signaling 'Dynamic flow capture service', pid 26879, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.569115 +03:  notifying bbe-smgd(63)
2022-02-01 12:07:29.569553 +03: signaling 'Enhanced Session Management process', pid 21107, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.569790 +03:  notifying smid(77)
2022-02-01 12:07:29.573410 +03: signaling 'Subscriber management process', pid 21086, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.574109 +03:  notifying transportd(81)
2022-02-01 12:07:29.577042 +03: signaling 'Transport control process', pid 26918, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.577664 +03:  notifying clksyncd(83)
2022-02-01 12:07:29.580129 +03: signaling 'Ethernet Clock Synchronization Process', pid 21079, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.580832 +03:  notifying sdk-vmmd(88)
2022-02-01 12:07:29.582469 +03: signaling 'App-engine virtual machine management process', pid 21030, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.582682 +03:  notifying kod(93)
2022-02-01 12:07:29.592742 +03: signaling 'Kernel offload daemon', pid 26895, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.604819 +03:  notifying scchassisd(100)
2022-02-01 12:07:29.605058 +03: cannot signal 'SCC Chassisd Process', signal 1, no pid
2022-02-01 12:07:29.605265 +03:  notifying lrmuxd(116)
2022-02-01 12:07:29.608483 +03: signaling 'Logical system multiplexer process', pid 26909, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.608668 +03:  notifying l2ald(121)
2022-02-01 12:07:29.618233 +03: signaling 'Layer 2 address flooding and learning process', pid 26867, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.618322 +03:  notifying l2cpd(123)
2022-02-01 12:07:29.622139 +03: signaling 'Layer 2 Control Protocol process', pid 26880, signal 1, status 0 with notification errors enabled
2022-02-01 12:07:29.696133 +03: ssync begins
2022-02-01 12:07:29.705907 +03: ssync ends
2022-02-01 12:07:29.706161 +03: gram_private_edit - start
2022-02-01 12:07:29.926005 +03: gram_private_edit - done
2022-02-01 12:07:30.32082 +03: commit complete
commit complete
```

</p>
</details>


#### commit full

```bash
"commit full" - скрытая команда. 
Нужна, чтобы проверить соответствие всего конфига.
Таким образом, все демоны(сервисы) - проверяют корректность своих настроек в соответствии со всем конфигом.
Это действие заставляет все сервисы проверить свои конфиги, а не только демоны, которые касаются только изменяемого куска конфига,
что делает просто "commit"
```

#### Пример commit full 
<details><summary>commit full | display detail</summary>
<p>


```bash
"Можно посмотреть в деталях,"
"что происходит под капотом внутри коробки при применения commit full"
"Там еще больше интересного, чем в commit )"

"commit full | display detail"
2023-01-07 15:20:42 GMT-3: Obtaining lock for commit
2023-01-07 15:20:42 GMT-3: merging latest committed configuration
2023-01-07 15:20:42 GMT-3: Using fast-diff method to generate diff
2023-01-07 15:20:43 GMT-3: start loading commit script changes
2023-01-07 15:20:43 GMT-3: no commit script changes
2023-01-07 15:20:43 GMT-3: no transient commit script changes
2023-01-07 15:20:43 GMT-3: finished loading commit script changes
2023-01-07 15:20:43 GMT-3: copying juniper.db to juniper.data+
2023-01-07 15:20:43 GMT-3: finished copying juniper.db to juniper.data+
2023-01-07 15:20:43 GMT-3: exporting juniper.conf
2023-01-07 15:20:43 GMT-3: expanding interface-ranges
2023-01-07 15:20:43 GMT-3: finished expanding interface-ranges
2023-01-07 15:20:43 GMT-3: expanding groups
2023-01-07 15:20:43 GMT-3: finished expanding groups
2023-01-07 15:20:43 GMT-3: setup foreign files
2023-01-07 15:20:47 GMT-3: update license counters
2023-01-07 15:20:47 GMT-3: finish license counters
2023-01-07 15:20:47 GMT-3: propagating foreign files
2023-01-07 15:20:47 GMT-3: cdg returns = 8(Can not execute binary)
2023-01-07 15:20:49 GMT-3: complete foreign files
2023-01-07 15:20:49 GMT-3: dropping unchanged foreign files
2023-01-07 15:20:49 GMT-3: executing 'ffp propagate'
2023-01-07 15:20:49 GMT-3: daemons checking new configuration
2023-01-07 15:20:49 GMT-3: Spawning Routing protocols process to check new configuration
2023-01-07 15:20:49 GMT-3: Collecting status of Routing protocols process
2023-01-07 15:20:49 GMT-3: Spawning Init daemon to check new configuration
2023-01-07 15:20:49 GMT-3: Collecting status of Init daemon
2023-01-07 15:20:49 GMT-3: Spawning Chassis control process to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of Chassis control process
2023-01-07 15:20:50 GMT-3: Spawning Booting daemon to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of Booting daemon
2023-01-07 15:20:50 GMT-3: Spawning IPSec Key Management daemon to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of IPSec Key Management daemon
2023-01-07 15:20:50 GMT-3: Spawning PKI service daemon to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of PKI service daemon
2023-01-07 15:20:50 GMT-3: Spawning Secure Neighbor Discovery Protocol process to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of Secure Neighbor Discovery Protocol process
2023-01-07 15:20:50 GMT-3: Spawning Firewall process to check new configuration
2023-01-07 15:20:50 GMT-3: Collecting status of Firewall process
2023-01-07 15:20:50 GMT-3: sending commit-check command to Interface control process(1443)
2023-01-07 15:20:51 GMT-3: Collecting status of Interface control process
2023-01-07 15:20:51 GMT-3: Spawning Simple Network Management Protocol process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Simple Network Management Protocol process
2023-01-07 15:20:51 GMT-3: Spawning Management Information Base II process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Management Information Base II process
2023-01-07 15:20:51 GMT-3: Spawning Virtual Router Redundancy Protocol process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Virtual Router Redundancy Protocol process
2023-01-07 15:20:51 GMT-3: Spawning Alarm control process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Alarm control process
2023-01-07 15:20:51 GMT-3: Spawning Packet Forwarding Engine statistics management process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Packet Forwarding Engine statistics management process
2023-01-07 15:20:51 GMT-3: Spawning Remote operations process to check new configuration
2023-01-07 15:20:51 GMT-3: Collecting status of Remote operations process
2023-01-07 15:20:51 GMT-3: Spawning Class-of-service process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Class-of-service process
2023-01-07 15:20:52 GMT-3: Spawning Port forwarding process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Port forwarding process
2023-01-07 15:20:52 GMT-3: Spawning Web management gatekeeper process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Web management gatekeeper process
2023-01-07 15:20:52 GMT-3: Spawning Dynamic Host Configuration Protocol process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Dynamic Host Configuration Protocol process
2023-01-07 15:20:52 GMT-3: Spawning Redundancy interface management process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Redundancy interface management process
2023-01-07 15:20:52 GMT-3: Spawning Link Aggregation Control Protocol process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Link Aggregation Control Protocol process
2023-01-07 15:20:52 GMT-3: Spawning Connectivity fault management process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Connectivity fault management process
2023-01-07 15:20:52 GMT-3: Spawning Junos Dynamic Host Configuration Protocol process to check new configuration
2023-01-07 15:20:52 GMT-3: Collecting status of Junos Dynamic Host Configuration Protocol process
2023-01-07 15:20:52 GMT-3: Spawning General authentication process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of General authentication process
2023-01-07 15:20:53 GMT-3: Spawning Database Replication process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Database Replication process
2023-01-07 15:20:53 GMT-3: Spawning Resource cleanup process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Resource cleanup process
2023-01-07 15:20:53 GMT-3: Spawning Shared memory routing socket message database process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Shared memory routing socket message database process
2023-01-07 15:20:53 GMT-3: Spawning Subscriber management process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Subscriber management process
2023-01-07 15:20:53 GMT-3: Spawning Flow Sampling (Sflow) Daemon to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Flow Sampling (Sflow) Daemon
2023-01-07 15:20:53 GMT-3: Spawning Port based Network Access Control to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Port based Network Access Control
2023-01-07 15:20:53 GMT-3: Spawning Unified access control daemon to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Unified access control daemon
2023-01-07 15:20:53 GMT-3: Spawning Uplink failure detection process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Uplink failure detection process
2023-01-07 15:20:53 GMT-3: Spawning Logical system multiplexer process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Logical system multiplexer process
2023-01-07 15:20:53 GMT-3: Spawning Pragmatic General Multicast process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Pragmatic General Multicast process
2023-01-07 15:20:53 GMT-3: Spawning Bidirectional Forwarding Detection process to check new configuration
2023-01-07 15:20:53 GMT-3: Collecting status of Bidirectional Forwarding Detection process
2023-01-07 15:20:53 GMT-3: Spawning Service Deployment System (SDX) process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Service Deployment System (SDX) process
2023-01-07 15:20:54 GMT-3: Spawning Audit process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Audit process
2023-01-07 15:20:54 GMT-3: Spawning Event processing process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Event processing process
2023-01-07 15:20:54 GMT-3: Spawning Multicast Snooping process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Multicast Snooping process
2023-01-07 15:20:54 GMT-3: Spawning Feature license management process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Feature license management process
2023-01-07 15:20:54 GMT-3: Spawning Junos node unifier process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Junos node unifier process
2023-01-07 15:20:54 GMT-3: Spawning Link Layer Discovery Protocol to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Link Layer Discovery Protocol
2023-01-07 15:20:54 GMT-3: Spawning Ethernet Switching Process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Ethernet Switching Process
2023-01-07 15:20:54 GMT-3: Spawning Port based Network Access Control to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Port based Network Access Control
2023-01-07 15:20:54 GMT-3: Spawning Autoinstallation process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Autoinstallation process
2023-01-07 15:20:54 GMT-3: Spawning Connectivity fault management process to check new configuration
2023-01-07 15:20:54 GMT-3: Collecting status of Connectivity fault management process
2023-01-07 15:20:54 GMT-3: Spawning PKI service daemon to check new configuration
2023-01-07 15:20:55 GMT-3: Collecting status of PKI service daemon
2023-01-07 15:20:55 GMT-3: Spawning Chassis Manager to check new configuration
2023-01-07 15:20:55 GMT-3: Collecting status of Chassis Manager
2023-01-07 15:20:55 GMT-3: Spawning Virtual Chassis Control Protocol to check new configuration
2023-01-07 15:20:55 GMT-3: Collecting status of Virtual Chassis Control Protocol
2023-01-07 15:20:55 GMT-3: updating shared candidate configuration
2023-01-07 15:20:55 GMT-3: updating commit revision
configuration check succeeds
2023-01-07 15:20:55 GMT-3: executing 'ffp synchronize'
2023-01-07 15:20:55 GMT-3: commit wrapup...
2023-01-07 15:20:55 GMT-3: activating '/var/etc/init.inc'
2023-01-07 15:20:56 GMT-3: activating '/var/etc/ntp.conf'
2023-01-07 15:20:56 GMT-3: activating '/var/etc/bootpd.conf'
2023-01-07 15:20:56 GMT-3: activating '/var/etc/cosd.conf'
2023-01-07 15:20:57 GMT-3: start ffp activate
2023-01-07 15:20:57 GMT-3: executing 'ffp activate'
2023-01-07 15:20:57 GMT-3: activating '/var/etc/master.passwd'
2023-01-07 15:20:58 GMT-3: activating '/var/etc/inetd.conf'
2023-01-07 15:20:59 GMT-3: activating '/var/etc/rc.conf.inc'
2023-01-07 15:20:59 GMT-3: activating '/var/etc/keyadmin.conf'
2023-01-07 15:20:59 GMT-3: activating '/var/etc/sysids'
2023-01-07 15:20:59 GMT-3: activating '/var/etc/login.conf'
2023-01-07 15:21:00 GMT-3: activating '/var/etc/ttys'
2023-01-07 15:21:00 GMT-3: activating '/var/etc/resolv.conf'
2023-01-07 15:21:01 GMT-3: activating '/var/etc/hosts'
2023-01-07 15:21:01 GMT-3: activating '/var/etc/group'
2023-01-07 15:21:01 GMT-3: activating '/var/etc/ntp.keys'
2023-01-07 15:21:01 GMT-3: activating '/var/etc/newsyslog.conf'
2023-01-07 15:21:01 GMT-3: activating '/var/etc/pam.conf'
2023-01-07 15:21:02 GMT-3: activating '/var/etc/pam_radius.conf'
2023-01-07 15:21:02 GMT-3: activating '/var/etc/pam_tacplus.conf'
2023-01-07 15:21:03 GMT-3: activating '/var/etc/ifinfo.conf'
2023-01-07 15:21:04 GMT-3: activating '/var/etc/issue'
2023-01-07 15:21:04 GMT-3: activating '/var/etc/sshd_conf'
2023-01-07 15:21:04 GMT-3: activating '/var/etc/ssh_config'
2023-01-07 15:21:04 GMT-3: activating '/var/etc/certs'
2023-01-07 15:21:08 GMT-3: activating '/var/etc/motd'
2023-01-07 15:21:09 GMT-3: activating '/var/etc/tzset'
2023-01-07 15:21:09 GMT-3: activating '/var/etc/cosd.conf.id'
2023-01-07 15:21:10 GMT-3: activating '/var/etc/named.conf'
2023-01-07 15:21:10 GMT-3: activating '/var/etc/crontab'
2023-01-07 15:21:10 GMT-3: activating '/var/etc/ifmdata.conf'
2023-01-07 15:21:10 GMT-3: activating '/var/etc/max-db-size-cfg'
2023-01-07 15:21:10 GMT-3: activating '/var/etc/subs-mgmt-cfg'
2023-01-07 15:21:11 GMT-3: activating '/var/etc/vmm.conf'
2023-01-07 15:21:11 GMT-3: activating '/var/etc/db_ext-cfg'
2023-01-07 15:21:12 GMT-3: activating '/var/etc/stunnel.conf'
2023-01-07 15:21:14 GMT-3: activating '/var/etc/lighttpd.conf'
2023-01-07 15:21:14 GMT-3: activating '/var/etc/lighttpd.server.pem'
2023-01-07 15:21:14 GMT-3: activating '/var/etc/lighttpd.ca.cert'
2023-01-07 15:21:14 GMT-3: activating '/var/etc/lighttpd.addresses'
2023-01-07 15:21:15 GMT-3: activating '/var/etc/ephinst.conf'
2023-01-07 15:21:15 GMT-3: executing foreign_commands
2023-01-07 15:21:15 GMT-3: /bin/sh /etc/rc.ui ui_setup_users (sh)
2023-01-07 15:21:15 GMT-3: executing ui_commit in rc.ui
2023-01-07 15:21:20 GMT-3: finish ffp activate
2023-01-07 15:21:20 GMT-3: copying configuration to juniper.save
2023-01-07 15:21:20 GMT-3: db_check_constraint_ids_clear start
2023-01-07 15:21:20 GMT-3: db_check_constraint_ids_clear done
2023-01-07 15:21:20 GMT-3: db_groups_info_clear start
2023-01-07 15:21:20 GMT-3: db_groups_info_clear done
2023-01-07 15:21:20 GMT-3: activating '/var/run/db/juniper.data'
2023-01-07 15:21:20 GMT-3: Rotate backup configs
2023-01-07 15:21:26 GMT-3: ssync begins
2023-01-07 15:21:26 GMT-3: ssync ends
2023-01-07 15:21:26 GMT-3: notifying daemons of new configuration
2023-01-07 15:21:26 GMT-3:  notifying none(0)
2023-01-07 15:21:26 GMT-3: cannot signal 'None process', signal 1, no pid
2023-01-07 15:21:26 GMT-3:  notifying mgd(1)
2023-01-07 15:21:26 GMT-3: signaling 'Management process', pid 1421, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:26 GMT-3:  notifying rpd(2)
2023-01-07 15:21:26 GMT-3: signaling 'Routing protocols process', pid 1452, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:26 GMT-3:  notifying cli(3)
2023-01-07 15:21:26 GMT-3: cannot signal 'Command-line interface', signal 1, no pid
2023-01-07 15:21:26 GMT-3:  notifying init(4)
2023-01-07 15:21:26 GMT-3: signaling 'Init daemon', pid 1, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:26 GMT-3:  notifying chassisd(5)
2023-01-07 15:21:28 GMT-3: signaling 'Chassis control process', pid 1444, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying xntpd(6)
2023-01-07 15:21:28 GMT-3: signaling 'Network time process', pid 1449, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying bootpd(7)
2023-01-07 15:21:28 GMT-3: cannot signal 'Booting daemon', signal 1, no pid
2023-01-07 15:21:28 GMT-3:  notifying kmd(8)
2023-01-07 15:21:28 GMT-3: cannot signal 'IPSec Key Management daemon', signal 1, no pid
2023-01-07 15:21:28 GMT-3:  notifying pkid(10)
2023-01-07 15:21:28 GMT-3: signaling 'PKI service daemon', pid 1468, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying sendd(11)
2023-01-07 15:21:28 GMT-3: signaling 'Secure Neighbor Discovery Protocol process', pid 1465, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying dfwd(12)
2023-01-07 15:21:28 GMT-3: signaling 'Firewall process', pid 1458, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying dcd(13)
2023-01-07 15:21:28 GMT-3: signaling 'Interface control process', pid 1443, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying snmpd(14)
2023-01-07 15:21:28 GMT-3: signaling 'Simple Network Management Protocol process', pid 1450, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:28 GMT-3:  notifying mib2d(15)
2023-01-07 15:21:29 GMT-3: signaling 'Management Information Base II process', pid 1451, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:29 GMT-3:  notifying vrrpd(16)
2023-01-07 15:21:30 GMT-3: cannot signal 'Virtual Router Redundancy Protocol process', signal 1, no pid
2023-01-07 15:21:30 GMT-3:  notifying alarmd(17)
2023-01-07 15:21:30 GMT-3: signaling 'Alarm control process', pid 1445, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:30 GMT-3:  notifying pfed(18)
2023-01-07 15:21:31 GMT-3: signaling 'Packet Forwarding Engine statistics management process', pid 1454, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:31 GMT-3:  notifying rmopd(21)
2023-01-07 15:21:32 GMT-3: signaling 'Remote operations process', pid 1455, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:32 GMT-3:  notifying cosd(22)
2023-01-07 15:21:32 GMT-3: signaling 'Class-of-service process', pid 1456, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:32 GMT-3:  notifying fud(23)
2023-01-07 15:21:33 GMT-3: cannot signal 'Port forwarding process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying ksyncd(24)
2023-01-07 15:21:33 GMT-3: cannot signal 'Kernel replication process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying inetd(25)
2023-01-07 15:21:33 GMT-3: signaling 'Inet process', pid 1422, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying httpd-gk(26)
2023-01-07 15:21:33 GMT-3: cannot signal 'Web management gatekeeper process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying dhcpd(27)
2023-01-07 15:21:33 GMT-3: cannot signal 'Dynamic Host Configuration Protocol process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying rdd(28)
2023-01-07 15:21:33 GMT-3: signaling 'Redundancy interface management process', pid 1461, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying lacpd(29)
2023-01-07 15:21:33 GMT-3: signaling 'Link Aggregation Control Protocol process', pid 1464, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying lfmd(30)
2023-01-07 15:21:33 GMT-3: signaling 'Ethernet OAM Link-Fault-Management process', pid 1447, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying tnetd(32)
2023-01-07 15:21:33 GMT-3: cannot signal 'TNP process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying cfmd(33)
2023-01-07 15:21:33 GMT-3: signaling 'Connectivity fault management process', pid 1448, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying jdhcpd(34)
2023-01-07 15:21:33 GMT-3: signaling 'Junos Dynamic Host Configuration Protocol process', pid 1469, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying authd(35)
2023-01-07 15:21:33 GMT-3: signaling 'General authentication process', pid 1470, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying rpcbind(36)
2023-01-07 15:21:33 GMT-3: cannot signal 'Service for rpc requests', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying mountd(37)
2023-01-07 15:21:33 GMT-3: cannot signal 'Service for NFS mounts requests', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying bdbrepd(38)
2023-01-07 15:21:33 GMT-3: signaling 'Database Replication process', pid 1462, signal 1, status 0 with notification errors disabled
2023-01-07 15:21:33 GMT-3:  notifying res-cleanupd(39)
2023-01-07 15:21:33 GMT-3: cannot signal 'Resource cleanup process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying shm-rtsdbd(40)
2023-01-07 15:21:33 GMT-3: signaling 'Shared memory routing socket message database process', pid 1467, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying datapath-traced(41)
2023-01-07 15:21:33 GMT-3: cannot signal 'DATAPATH Trace process', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying smid(42)
2023-01-07 15:21:33 GMT-3: signaling 'Subscriber management process', pid 1460, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying gstatd(44)
2023-01-07 15:21:33 GMT-3: cannot signal 'geom-device performance monitoring daemon', signal 1, no pid
2023-01-07 15:21:33 GMT-3:  notifying sflowd(45)
2023-01-07 15:21:33 GMT-3: signaling 'Flow Sampling (Sflow) Daemon', pid 1473, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying dot1xd(46)
2023-01-07 15:21:33 GMT-3: signaling 'Port based Network Access Control', pid 1474, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:33 GMT-3:  notifying uacd(47)
2023-01-07 15:21:34 GMT-3: cannot signal 'Unified access control daemon', signal 1, no pid
2023-01-07 15:21:34 GMT-3:  notifying ufdd(50)
2023-01-07 15:21:34 GMT-3: cannot signal 'Uplink failure detection process', signal 1, no pid
2023-01-07 15:21:34 GMT-3:  notifying ppmd(52)
2023-01-07 15:21:34 GMT-3: signaling 'Periodic packet management process', pid 1457, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying lrmuxd(54)
2023-01-07 15:21:34 GMT-3: cannot signal 'Logical system multiplexer process', signal 1, no pid
2023-01-07 15:21:34 GMT-3:  notifying pgmd(57)
2023-01-07 15:21:34 GMT-3: cannot signal 'Pragmatic General Multicast process', signal 1, no pid
2023-01-07 15:21:34 GMT-3:  notifying bfdd(58)
2023-01-07 15:21:34 GMT-3: signaling 'Bidirectional Forwarding Detection process', pid 1459, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying sdxd(59)
2023-01-07 15:21:34 GMT-3: cannot signal 'Service Deployment System (SDX) process', signal 1, no pid
2023-01-07 15:21:34 GMT-3:  notifying auditd(60)
2023-01-07 15:21:34 GMT-3: signaling 'Audit process', pid 1463, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying eventd(62)
2023-01-07 15:21:34 GMT-3: signaling 'Event processing process', pid 1070, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying mcsnoopd(63)
2023-01-07 15:21:34 GMT-3: signaling 'Multicast Snooping process', pid 1475, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying license-check(65)
2023-01-07 15:21:34 GMT-3: signaling 'Feature license management process', pid 1476, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:34 GMT-3:  notifying commitd(66)
2023-01-07 15:21:35 GMT-3: cannot signal 'Ephemeral Controller Daemon', signal 1, no pid
2023-01-07 15:21:35 GMT-3:  notifying jnud(67)
2023-01-07 15:21:35 GMT-3: cannot signal 'Junos node unifier process', signal 1, no pid
2023-01-07 15:21:35 GMT-3:  notifying rest-api(68)
2023-01-07 15:21:35 GMT-3: cannot signal 'REST Application Programming Interface process', signal 1, no pid
2023-01-07 15:21:35 GMT-3:  notifying ntad(69)
2023-01-07 15:21:36 GMT-3: cannot signal 'Network Topology Abstraction Daemon', signal 1, no pid
2023-01-07 15:21:36 GMT-3:  notifying lldpd(70)
2023-01-07 15:21:36 GMT-3: signaling 'Link Layer Discovery Protocol', pid 1477, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:36 GMT-3:  notifying lfmd(71)
2023-01-07 15:21:37 GMT-3: signaling 'Ethernet OAM Link-Fault-Management process', pid 1447, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:37 GMT-3:  notifying eswd(72)
2023-01-07 15:21:37 GMT-3: signaling 'Ethernet Switching Process', pid 1466, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:37 GMT-3:  notifying dot1xd(73)
2023-01-07 15:21:38 GMT-3: signaling 'Port based Network Access Control', pid 1474, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:38 GMT-3:  notifying autod(74)
2023-01-07 15:21:38 GMT-3: cannot signal 'Autoinstallation process', signal 1, no pid
2023-01-07 15:21:38 GMT-3:  notifying cfmd(75)
2023-01-07 15:21:38 GMT-3: signaling 'Connectivity fault management process', pid 1448, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:38 GMT-3:  notifying pkid(76)
2023-01-07 15:21:38 GMT-3: signaling 'PKI service daemon', pid 1468, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:38 GMT-3:  notifying chassism(77)
2023-01-07 15:21:38 GMT-3: signaling 'Chassis Manager', pid 1424, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:38 GMT-3:  notifying sfid(78)
2023-01-07 15:21:38 GMT-3: signaling 'Software Forwarding', pid 1425, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:38 GMT-3:  notifying vccpd(79)
2023-01-07 15:21:39 GMT-3: signaling 'Virtual Chassis Control Protocol', pid 1426, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:39 GMT-3:  notifying pfem(80)
2023-01-07 15:21:39 GMT-3: signaling 'Packet Forwarding Engine Manager', pid 1427, signal 1, status 0 with notification errors enabled
2023-01-07 15:21:40 GMT-3: ssync begins
2023-01-07 15:21:41 GMT-3: ssync ends
2023-01-07 15:21:41 GMT-3: gram_private_edit - start
2023-01-07 15:21:43 GMT-3: gram_private_edit - done
2023-01-07 15:21:43 GMT-3: commit complete
commit complete

```

</p>
</details>


[дока про "commit full"](https://kb.juniper.net/InfoCenter/index?page=content&id=KB15410#:~:text=Solution%3A,ve%20changed%20the%20config%20significantly.)


#### commit confirm [minite]

```bash
"Применить изменения с их автоматическим откатом через определенное время"
"Очень удобно если не уверен, что твой комит не поломает доступ к устройству."
"Например команда полезна при работах на правилах copp на firewall."
commit confirm 5
"если всё плохо - через 5 минут конфиг откатится сам назад."

"если все хорошо, то можно сделать просто "commit" сохранив таким образом существующую конфигурацию."
commit
```

#### show system commit 
```bash
"Просмотр существующих коммиттов"
show system commit 
    0   2022-02-16 21:55:12 +03 by user1 via cli commit synchronize
    1   2022-02-16 18:47:49 +03 by user2 via cli commit synchronize
    2   2022-02-16 18:34:24 +03 by user3 via cli commit synchronize
    3   2022-02-16 18:32:14 +03 by user4 via cli commit synchronize
```

#### Просмотр диффа между коммиттами
##### Пример 1 

```bash
"Например, если последним действием поменяли local-preference с 200 на 250,"
"то для просмотра, что именно поменялось можно использовать конструкция такого типа:" 
show system rollback compare 1 0
[edit policy-options policy-statement policy1 term local-preference then]
-      local-preference 200;
+      local-preference 250;
```

##### Пример 2
```bash
"Соответственно, чтобы посмотреть что было изменено во 2ом коммите,"
"то можно использовать конструкция такого типа:"
show system rollback compare 2 1
    [edit interfaces ae0 unit 1111 family ccc policer]
    +       input 500Mbit;

"В данном случае во втором коммитте был добавлен только полисер на 500Mbit"
"больше ничего не изменялось."
```


#### Просмотр определения SFP модулей

<details><summary>show chassis hardware </summary>
<p>

```bash
"show chassis hardware"
    Hardware inventory:
    Item             Version  Part number  Serial number     Description
    Chassis                                xxxxxxxxxxxxx     MX960
    Midplane         REV 04   750-047853   ACRC5864          Enhanced MX960 Backplane
    Fan Extender     REV 02   710-018051   CACF3080          Extended Cable Manager
    FPM Board        REV 03   710-014974   CACW7761          Front Panel Display
    PDM              Rev 03   740-013110   QCS1743504L       Power Distribution Module
    PEM 0            Rev 11   740-027760   QCS1740N1AF       PS 4.1kW; 200-240V AC in
    PEM 1            Rev 11   740-027760   QCS1810N02J       PS 4.1kW; 200-240V AC in
    PEM 2            Rev 11   740-027760   QCS1828N0M4       PS 4.1kW; 200-240V AC in
    PEM 3            Rev 11   740-027760   QCS1803N06W       PS 4.1kW; 200-240V AC in
    Routing Engine 0 REV 08   750-072923   CARM8425          RE-S-2X00x6
    Routing Engine 1 REV 08   750-072923   CARX5296          RE-S-2X00x6
    CB 0             REV 13   750-062572   CARP0477          Enhanced MX SCB 2
    CB 1             REV 15   750-062572   CARZ5165          Enhanced MX SCB 2
    FPC 0            REV 10   750-063181   CAJM4835          MPC3E NG PQ & Flex Q
    CPU            REV 13   711-045719   CAJL1109          RMPC PMB
    MIC 0          REV 36   750-028392   CAHP1766          3D 20x 1GE(LAN) SFP
        PIC 0                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
        Xcvr 0              NON-JNPR     PC3K350482        SFP-LX10
        Xcvr 1              NON-JNPR     W1806262166       SFP-FC1G-LW
        Xcvr 2     }        NON-JNPR     W20042016938      SFP-FC1G-LW
        Xcvr 3              NON-JNPR     PB8V093240        SFP-LX10
        Xcvr 4              NON-JNPR     H17A16130281      SFP-LX10
        Xcvr 5              NON-JNPR     P13A00500167      SFP-LX10
        Xcvr 6              NON-JNPR     C17A11050035      SFP-LX10
        Xcvr 7              NON-JNPR     S14A05420163      SFP-LX10
        Xcvr 8     2K       NON-JNPR     W2104080422       SFP-FC1G-LW
        Xcvr 9     tPh      NON-JNPR     W19111207024      SFP-FC1G-LW
        PIC 1                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
        Xcvr 0              NON-JNPR     C1005040443       UNSUPPORTED
        Xcvr 1     v        NON-JNPR     C1102180518       SFP-LR
        Xcvr 2              NON-JNPR     SAAA410006        UNSUPPORTED
        Xcvr 3              NON-JNPR     C1012010120       UNSUPPORTED
        Xcvr 4              NON-JNPR     SD4G670550        SFP-LX10
        Xcvr 5     ,        NON-JNPR     W2108246604       SFP-FC1G-LW
        Xcvr 6     O[Y+     NON-JNPR     5300048           SFP-EX
        Xcvr 7              NON-JNPR     C16A08390084      SFP-LX10
        Xcvr 8              NON-JNPR     PB8P170054        SFP-LX10
        Xcvr 9              NON-JNPR     0711021785        SFP-1000BASE-BX10-U
    MIC 1          REV 36   750-028392   CAHA0737          3D 20x 1GE(LAN) SFP
        PIC 2                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
        Xcvr 5              NON-JNPR     C16A08390098      SFP-LX10
        Xcvr 6              NON-JNPR     SD6V310622        SFP-LX10
        Xcvr 7     OMPATI   NON-JNPR     9420989017        SFP-EX
        Xcvr 8     g        NON-JNPR     FT201310171745    SFP-FC1G-LW
        PIC 3                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
    FPC 1            REV 10   750-063181   CAJK5471          MPC3E NG PQ & Flex Q
    CPU            REV 13   711-045719   CAJK0087          RMPC PMB
    MIC 0          REV 25   750-028392   ZB3807            3D 20x 1GE(LAN) SFP
        PIC 0                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
        PIC 1                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
    MIC 1          REV 22   750-028392   YM6347            3D 20x 1GE(LAN) SFP
        PIC 2                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
        PIC 3                 BUILTIN      BUILTIN           10x 1GE(LAN) SFP
    FPC 3            REV 31   750-037358   CAEH9485          MPC4E 3D 32XGE
    CPU            REV 10   711-035209   CAEN7738          HMPC PMB 2G 
    PIC 0                   BUILTIN      BUILTIN           8X10GE SFPP                                                                                                                                                                
        Xcvr 0       REV 01   740-021308   PM2NG41211        SFP+-10G-LR
        Xcvr 1       REV 01   740-021308   PM2NG41213        SFP+-10G-LR
        Xcvr 2       REV 01   740-021308   PM2NG41212        SFP+-10G-LR
        Xcvr 3       REV 01   740-021308   PM2NG41218        SFP+-10G-LR
    PIC 1                   BUILTIN      BUILTIN           8X10GE SFPP
        Xcvr 0       REV 01   740-021309   W2112140548       SFP+-10G-LR
        Xcvr 1       REV 01   740-021309   ASELRN109         SFP+-10G-LR
    PIC 2                   BUILTIN      BUILTIN           8X10GE SFPP
        Xcvr 0       REV 01   740-021308   PM2NG41216        SFP+-10G-LR
    PIC 3                   BUILTIN      BUILTIN           8X10GE SFPP
        Xcvr 0                NON-JNPR     SD7W300018        SFP+-10G-SR
    FPC 9            REV 09   750-063181   CAJF3149          MPC3E NG PQ & Flex Q
    CPU            REV 13   711-045719   CAJE9808          RMPC PMB
    MIC 0          REV 15   750-028390   CABE1696          3D 40x 1GE(LAN) RJ45
        PIC 0                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 1                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 2                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 3                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
    FPC 10           REV 10   750-063181   CAJM4603          MPC3E NG PQ & Flex Q
    CPU            REV 13   711-045719   CAJL1464          RMPC PMB
    MIC 0          REV 15   750-028390   CABE1690          3D 40x 1GE(LAN) RJ45
        PIC 0                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 1                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 2                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 3                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
    FPC 11           REV 10   750-063181   CAJM4545          MPC3E NG PQ & Flex Q
    CPU            REV 13   711-045719   CAJL1494          RMPC PMB
    MIC 0          REV 15   750-028390   CABE1693          3D 40x 1GE(LAN) RJ45
        PIC 0                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 1                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 2                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
        PIC 3                 BUILTIN      BUILTIN           10x 1GE(LAN) RJ45
    Fan Tray 0       REV 08   740-031521   ACDB2692          Enhanced Fan Tray
    Fan Tray 1       REV 08   740-031521   ACDB2398          Enhanced Fan Tray
```

</p>
</details>

#### Просмотр уровней для оптических модулей

##### Без матчинга

<details><summary>show interfaces diagnostics optics ge-0/0/1 </summary>
<p>

```bash
"show interfaces diagnostics optics ge-0/0/1"                      
    Physical interface: ge-0/0/1
        Laser bias current                        :  19.216 mA
        Laser output power                        :  0.2190 mW / -6.60 dBm
        Module temperature                        :  20 degrees C / 67 degrees F
        Module voltage                            :  3.3690 V
        Laser receiver power                      :  0.0398 mW / -14.00 dBm
        Laser bias current high alarm             :  Off
        Laser bias current low alarm              :  Off
        Laser bias current high warning           :  Off
        Laser bias current low warning            :  Off
        Laser output power high alarm             :  Off
        Laser output power low alarm              :  Off
        Laser output power high warning           :  Off
        Laser output power low warning            :  Off
        Module temperature high alarm             :  Off
        Module temperature low alarm              :  Off
        Module temperature high warning           :  Off
        Module temperature low warning            :  Off
        Module voltage high alarm                 :  Off
        Module voltage low alarm                  :  Off
        Module voltage high warning               :  Off
        Module voltage low warning                :  Off
        Laser rx power high alarm                 :  Off
        Laser rx power low alarm                  :  Off
        Laser rx power high warning               :  Off
        Laser rx power low warning                :  Off
        Laser bias current high alarm threshold   :  80.000 mA
        Laser bias current low alarm threshold    :  10.000 mA
        Laser bias current high warning threshold :  75.000 mA
        Laser bias current low warning threshold  :  12.000 mA
        Laser output power high alarm threshold   :  1.2580 mW / 1.00 dBm
        Laser output power low alarm threshold    :  0.0500 mW / -13.01 dBm
        Laser output power high warning threshold :  1.0000 mW / 0.00 dBm
        Laser output power low warning threshold  :  0.0630 mW / -12.01 dBm
        Module temperature high alarm threshold   :  95 degrees C / 203 degrees F
        Module temperature low alarm threshold    :  -50 degrees C / -58 degrees F
        Module temperature high warning threshold :  90 degrees C / 194 degrees F
        Module temperature low warning threshold  :  -40 degrees C / -40 degrees F
        Module voltage high alarm threshold       :  3.900 V
        Module voltage low alarm threshold        :  2.700 V
        Module voltage high warning threshold     :  3.700 V
        Module voltage low warning threshold      :  2.900 V
        Laser rx power high alarm threshold       :  0.6310 mW / -2.00 dBm
        Laser rx power low alarm threshold        :  0.0010 mW / -30.00 dBm
        Laser rx power high warning threshold     :  0.5012 mW / -3.00 dBm
        Laser rx power low warning threshold      :  0.0013 mW / -28.86 dBm
```
</p>
</details>

##### С матчингом
<details><summary>show interfaces diagnostics optics ge-0/0/1 | match "rece|rx"</summary>
<p>

```bash
show interfaces diagnostics optics ge-0/0/1 | match "rece|rx"    
    Laser receiver power                      :  0.0398 mW / -14.00 dBm
    Laser rx power high alarm                 :  Off
    Laser rx power low alarm                  :  Off
    Laser rx power high warning               :  Off
    Laser rx power low warning                :  Off
    Laser rx power high alarm threshold       :  0.6310 mW / -2.00 dBm
    Laser rx power low alarm threshold        :  0.0010 mW / -30.00 dBm
    Laser rx power high warning threshold     :  0.5012 mW / -3.00 dBm
    Laser rx power low warning threshold      :  0.0013 mW / -28.86 dBm
```
</p>
</details>

<details><summary>show interfaces diagnostics optics ge-0/0/1 | match "Laser o"</summary>
<p>

```bash

show interfaces diagnostics optics ge-0/0/1 | match "Laser o"    
    Laser output power                        :  0.2190 mW / -6.60 dBm
    Laser output power high alarm             :  Off
    Laser output power low alarm              :  Off
    Laser output power high warning           :  Off
    Laser output power low warning            :  Off
    Laser output power high alarm threshold   :  1.2580 mW / 1.00 dBm
    Laser output power low alarm threshold    :  0.0500 mW / -13.01 dBm
    Laser output power high warning threshold :  1.0000 mW / 0.00 dBm
    Laser output power low warning threshold  :  0.0630 mW / -12.01 dBm
```
</p>
</details>


#### Сохранения конфига:

##### Способ №1
<details><summary> request system configuration rescue save</summary>
<p>

```bash
  "request system configuration rescue save"

  "сохраняет в /config"
  ls -la /config | grep rescue.conf.gz
    -rw-r-----   1 root  wheel  3030 Jan  3 11:33 rescue.conf.gz
```
</p>
</details>

##### Способ №2
<details><summary>save</summary>
<p>

```bash
    configure private
    save /var/tmp/ex-4200-24t.conf
```
</p>
</details>

#### Загрузка конфига из файла.
[лит-ра](https://www.juniper.net/documentation/ru/ru/software/junos/cli/topics/topic-map/junos-config-files-loading.html)
  
```bash  
"Из конфигурационного режима"
   load override /var/tmp/ex-4200-24t.conf
   show | compare 
```

#### Очистка конфига.
[лит-ра](https://nastroisam.ru/sbros-juniper-zavodskie-nastroyki/)
  
##### Способ №1
<details><summary>load factory defaults</summary>
<p>

```bash
  "Очищает только конфиг на дефолт"
  "load factory defaults"
    commit check 
    [edit]
      'system'
        Missing mandatory statement: 'root-authentication'
    error: configuration check-out failed: (missing mandatory statements)

    "нельзя провести коммит если не задан root учетка"
    set system root-authentication plain-text-password
    commit 

    "Не пускает под рутом, надо перезагрузиться тогда все будет ок"
    start shell          
        % su root
        su: who are you?
        % exit
```
</p>
</details>


##### Способ №2
<details><summary> request system zeroize</summary>
<p>

```bash
  "Очищает только конфиг + чистит диск от лишнего"

    "request system zeroize"
      warning: System will be rebooted and may not boot without configuration
      Erase all data, including configuration and log files? [yes,no] (no) yes 

      warning: ipsec-key-management subsystem not running - not needed by configuration.
      warning: zeroizing fpc0

      "!!! после перезагрузки пароль root пустой..."
      "для задания пароля после входя надо"
      "set system root-authentication plain-text-password"
```
</p>
</details>

##### Способ №3 - лайтовый
При этом способе просто удаляетс явсе содержимое конфига

```bash
"delete"
```

#### Заведение нового пользователя
```bash
    set system login user bar class super-user
    set system login user bar authentication plain-text-password
```

#### Корректно потушить Juniper
```bash
 request system halt
```

#### replace and copy

Если есть задачка перетащить настройки с одного интерфейса на другой или просто их скопировать, то 
в Juniper-е на помощь приходят команды "replace" и "copy"


Либо в более общем случае заменить один паттерн на другой (например,  определенный дескрипшен на другой, повторяющийся много раз в конфиге)


##### replace

``` bash
"replace pattern PATTERN1 with PATTERN2"

Работает как замена одного "PATTERN1" на "PATTERN2" во всей конфигурации
при этом "PATTERN2" не должно быть в конфиге иначе возникнет ошибка вида:

"user@MX# replace pattern ge-10/3/5 with ge-10/0/6"
  error: target statement 'ge-10/0/6' already exists
  [edit interfaces]
    'ge-10/3/5'
      could not rename to 'ge-10/0/6'

"user@MX> show configuration | display set | match ge-10/0/6"
  set interfaces ge-10/0/6 description "MY DESCRIPTION HERE"

Если упоминания ge-10/0/6 в конфиге нет, то тогда:

В данном куске конфига можно заменить "ge-10/3/5" на "ge-10/0/6"

"user@MX> show configuration | display set | match ge-10/3/5"
  set interfaces ge-10/3/5 description "!JP ISP"
  set interfaces ge-10/3/5 flexible-vlan-tagging
  set interfaces ge-10/3/5 mtu 9216
  set interfaces ge-10/3/5 encapsulation flexible-ethernet-services
  set interfaces ge-10/3/5 unit 999 description "L2VPN 999"
  set interfaces ge-10/3/5 unit 999 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 999 vlan-id 999
  set interfaces ge-10/3/5 unit 111 description "L2VPN 111"
  set interfaces ge-10/3/5 unit 111 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 111 vlan-id 111
  set routing-instances EVPN9 bridge-domains VL0999 interface ge-10/3/5.999
  set routing-instances EVPN1 bridge-domains VL0111 interface ge-10/3/5.111

"user@MX> configure private"                                     

"user@MX# replace pattern ge-10/3/5 with ge-10/0/6"    

ИТОГО:
"user@MX# show | compare" 
  [edit interfaces]
  +   ge-10/0/6 {
  +       description "!JP ISP";
  +       flexible-vlan-tagging;
  +       mtu 9216;
  +       encapsulation flexible-ethernet-services;
  +       unit 999 {
  +           description "L2VPN 999";
  +           encapsulation vlan-bridge;
  +           vlan-id 999;
  +       }
  +       unit 111 {
  +           description "L2VPN 111";
  +           encapsulation vlan-bridge;
  +           vlan-id 111;
  +       }
  +   }
  -   ge-10/3/5 {
  -       description "!JP ISP";
  -       flexible-vlan-tagging;
  -       mtu 9216;
  -       encapsulation flexible-ethernet-services;
  -       unit 999 {
  -           description "L2VPN 999";
  -           encapsulation vlan-bridge;
  -           vlan-id 999;
  -       }
  -       unit 111 {
  -           description "L2VPN 111";
  -           encapsulation vlan-bridge;
  -           vlan-id 111;
  -       }
  -   }
  [edit routing-instances EVPN9 bridge-domains VL0999]
  +      interface ge-10/0/6.999;
        interface ae1.999 { ... }
  [edit routing-instances EVPN9 bridge-domains VL0999]
  -      interface ge-10/3/5.999;
  [edit routing-instances EVPN1 bridge-domains VL0111]
  +      interface ge-10/0/6.111;
  -      interface ge-10/3/5.111;
```

##### copy interface

```bash 
"Работает как копирование настроек одного интерфейска на другой"

Скопируем настройки данного интерфейса на другой
"user@MX# run show configuration interfaces ge-10/3/5 | display set"
  set interfaces ge-10/3/5 description "!JP ISP"
  set interfaces ge-10/3/5 flexible-vlan-tagging
  set interfaces ge-10/3/5 mtu 9216
  set interfaces ge-10/3/5 encapsulation flexible-ethernet-services
  set interfaces ge-10/3/5 unit 999 description "L2VPN 999"
  set interfaces ge-10/3/5 unit 999 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 999 vlan-id 999
  set interfaces ge-10/3/5 unit 111 description "L2VPN 111"
  set interfaces ge-10/3/5 unit 111 encapsulation vlan-bridge
  set interfaces ge-10/3/5 unit 111 vlan-id 111

"user@MX# copy interfaces ge-10/3/5 to ge-10/0/6"

ИТОГО:
"user@MX# show | compare"                            
  [edit interfaces]
  +   ge-10/0/6 {
  +       description "!JP ISP";
  +       flexible-vlan-tagging;
  +       mtu 9216;
  +       encapsulation flexible-ethernet-services;
  +       unit 999 {
  +           description "L2VPN 999";
  +           encapsulation vlan-bridge;
  +           vlan-id 999;
  +       }
  +       unit 111 {
  +           description "L2VPN 111";
  +           encapsulation vlan-bridge;
  +           vlan-id 111;
  +       }
  +   }
```

## Мелкие "приколюхи"

### Чат между пользователями коробки

Список пользователей
```bash
"alex@Juniper> show system users"                                              
 5:40PM  up 458 days, 15:20, 6 users, load averages: 0.76, 0.88, 0.77
USER     TTY      FROM                              LOGIN@  IDLE WHAT
alex     pts/0    1.1.1.1                           10:41AM  3:09 -cl           
max      pts/1    1.1.1.1                           10:56AM  6:44 -cl           
vova     pts/2    1.1.1.1                           10:49AM  6:47 -cl           
kirill     pts/2    1.1.1.1                         10:49AM  6:47 -cl           
```

Пользоваьтель alex отправляет пользователь kirill сообщение:
```bash
"alex@Juniper> request message  message "hi there!" kirill"
```

Пользоваьтель kirill получает сообщение от пользователя alex:
```bash
kirill@Juniper> request message terminal                                              
Message from alex@Juniper on pts/5 at 17:14 ...
Hello WTFIGO! )
EOF
?
```
