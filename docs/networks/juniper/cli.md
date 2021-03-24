title: cli

#Основы Juniper cli

## Навигация

### Режимы работы: 

operational mode - операционный режим.
В нем можно посмотреть текущую конфигурации, текущее состояние протоколо IGP, BGP и пр.
```bash
admin@MBR>  
```

configuration mode - конфигурационный режим.
В нем, как понятно из названия, настраивается конфигурация
```bash
admin@MBR>configure
admin@MBR# 

# либо через edit
admin@MBR>edit
admin@MBR# 
```

shell - режим шелла.
По сути в нем просто проваливаешься в JunOS, каковая являтся запиленным и протюниным FreeBSD.
Внем есть набор стандартных доступных команд Unix, тип top, uname и пр...
P.S. midnight commander мне туда поставить не удалось )
```bash
admin@MBR>start shell
admin@MNSK-MBR0-new> start shell 
% uname -a
JUNOS MNSK-MBR0-new 19.4R3.11 JUNOS 19.4R3.11 #0: 2020-10-08 21:58:24 UTC     builder@qnc-jre-emake1t.juniper.net:/volume/build/junos/19.4/release/19.4R3.11/obj/powerpc/junos/bsd/kernels/JUNIPER-PPC/kernel  powerpc  
```

### Общая структура конфига 
    
Как правило конфиг состоит из следующих секций:

```bash
system {
}
chassis {
}
```
<details><summary>security { </summary>
<p>
<details><summary>Пример секции interfaces</summary>
<p>
```bash 
```
</p>
</details>

}
<details><summary>Пример секции interfaces</summary>
<p>
```bash 
interfaces {
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
                    address x.x.x.x/29;
                }
            }
            unit 900 {
                description "<< Desc900 >>";
                vlan-id 900;
                family inet {
                    address y.y.y.y/30;
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

```bash
snmp {
}
forwarding-options {
}
policy-options {
}
firewall {
}
routing-options {
}
protocols {
}
```

Каждая секция несет свой функционал.
Например в секции "interfaces" как не трудно догадаться из названия перечисляются интерфейсы как физические так и логические. 

!!! note "Заметка"
	У джуна портчаны называются aggregation ethernet, например ae0

<details><summary>Пример секции interfaces</summary>
<p>

```bash	
interfaces {
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
                    address x.x.x.x/29;
                }
            }
            unit 900 {
                description "<< Desc900 >>";
                vlan-id 900;
                family inet {
                    address y.y.y.y/30;
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

### Навигация по конфигу ВСЁ ОЧЕНЬ УДОБНО!!! )
```bash
# из operational mode
show configuration <секция_в_конфиге подсекция_в_конфиге под_подсекция_в_конфиге и т.д...> 

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
show <секция_в_конфиге подсекция_в_конфиге под_подсекция_в_конфиге и т.д...> 

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


### Редактирование конфига (я для себя определил три удобных режима)

#### 1. load merge/update terminal (relative)
     Загружает конфиг из терминала в нужную секцию

##### 1.1. load merge terminal relative
      Постим в нужном месте кусок своей конфигурации

#### 2. set 
      Перемещаемся в нужное место конфига

