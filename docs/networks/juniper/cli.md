title: Juniper cli

# Навигация

## Режимы работы: 
```bash
# operational mode
admin@MBR>  

# configuration mode
admin@MBR>configure
admin@MNSK-MBR0-new# 
```
## Общая структура конфига как правило состоит из следующих секций:
```bash
system {
}
chassis {
}
security {
}
interfaces {
}
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


```bash
##	из operational mode
show configuration <секция_в_конфиге подсекция_в_конфиге подсекция_подсекция_в_конфиге и тд> 
##	из operational mode
show <секция_в_конфиге подсекция_в_конфиге подсекция_подсекция_в_конфиге и тд> 
```


## Навигация по конфигу ВСЁ ОЧЕНЬ УДОБНО!!! )
```bash
##	из operational mode
show configuration <секция_в_конфиге подсекция_в_конфиге подсекция_подсекция_в_конфиге и тд> 
##	из operational mode
show <секция_в_конфиге подсекция_в_конфиге подсекция_подсекция_в_конфиге и тд> 
```

## Редактирование конфига (я для себя определил три удобных режима)

### 1. Переходим 


