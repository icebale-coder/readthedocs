title: cli

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


#### 1.1. load merge terminal relative 
      Добавляет конфигурацию путем слияния существущей и новой
      Постим в нужном месте кусок своей конфигурации

!!! Пример 1
<details><summary>Необходимо создать новый сабинтерфейс 900 в ae0</summary>
<p>
    

[] Заходим в configure mode и перемещаемся в секцию interfaces ae0

```bash
    admin@MBR>edit
    admin@MBR#edit interface ae0 

    [edit interfaces ae0]
```

[] Добавляем через терминал недостающую секцию конфига
Примечание: для того, чтобы завершить ввод в терминале необходимо ввести комбинацию Ctrl-D
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

[] Перемещаемся в корень конфига
```bash
admin@MBR# top 
[edit ]
```
[] Проверяем что поменяется в конфиге
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
[] Применяем изменения и выходим  в operation mode
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

#### 1.2. load update terminal relative
      Добавляет конфигурацию путем удаления существущей и добавлением новой
      Постим в нужном месте кусок своей конфигурации

#### 2. set 
      Перемещаемся в нужное место конфига, вносим новые параметры через команду "set"

