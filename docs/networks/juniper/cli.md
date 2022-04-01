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




