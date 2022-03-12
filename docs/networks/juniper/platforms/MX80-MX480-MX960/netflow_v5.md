# netflow v5 MX80

Настройка netflow v5 Juniper MX80

## Настройка секции forwarding-options

```bash
forwarding-options {
    sampling {
        sample-once;
        input {
            rate 1;
            run-length 0;
        }
        family inet {
            output {
                flow-server 10.10.10.10 {
                    port 9996;
                    source-address 1.1.1.1;
                    version 5;
                }
            }
        }
    }
}
```

## Настройка секции firewall
```bash
firewall {
    family inet {
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
}
```

## Настройка интерфейса, с которого нужно снимать netflow
```bash
interfaces {
	ae0 {
		unit 10 {
            description "<< Subinterface for netflow accounting>>";
            vlan-id 10;
            family inet {
                filter {
                    input SAMPLE;
                    output SAMPLE;
                }
                address x.x.x.x/31;
            }
        }
    }
}
```

!!! note "ip адреса"
```
	10.10.10.10 - адрес flow коллектора
	1.1.1.1 - адрес источника, от которого идет netflow на коллектор 
	(обычно указывается lo0 устройства)
```


Документация Juniper по настройке [netflow](https://www.juniper.net/documentation/en_US/junos/information-products/pathway-pages/services-interfaces/flow-monitoring.pdf) 
