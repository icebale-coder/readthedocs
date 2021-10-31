title: PMTUD

# PMTUD
PMTUD - Path MTU Discovery позволяет увеличить скорость принятия анонсов по BGP, например при приеме или отправки FullView

Для увеличения скорости, а по сути в данном контексте под скоростью подразумевается размер MTU и в конечном счете значение MSS в tcp сессии используется механизм [PMTUD](https://icebale.readthedocs.io/en/latest/networks/protocols-tech/TCP/#path_mtu_discovery_pmtud)

Для этого нужно:
1. Максимально увеличить MTU на интерфейсе в сторону соседа
2. Включить Path-MTU-Discovery


## Juniper [mtu-discovery]

[Изначально при установлении tcp соединения по порту 179 для BGP по дефолту MSS устанавливается равным 512 byte. Это делается для того, чтобы минимизировать возможность фрагментации пакета при прохождении промежуточных маршрутизаторов, где возможно выставлен меньший MTU.](https://www.juniper.net/documentation/us/en/software/junos/bgp/topics/ref/statement/mtu-discovery-edit-protocols-bgp.html)

У Джунипера по умолчанию PMTUD [включен](https://www.juniper.net/documentation/us/en/software/junos/bgp/topics/ref/statement/mtu-discovery-edit-protocols-bgp.html)
```bash
system internet-options{
   mtu-discovery
}
interfaces {
  ae0 {
      mtu 9000;
  }
}
```

## Cisco [tcp path-mtu-discovery]
У Циско по умолчагнию PMTUD [включен](https://www.cisco.com/c/dam/en/us/td/docs/routers/asr920/configuration/guide/iproute/16-8-1/b-iproute-xe-16-8-1-asr920.html)
```bash
tcp path-mtu-discovery

interface Bundle-Ether1.1111
  ! Размер MTU нужно устанавливать уже непосредственно на интерфейсах/сабинтерфейсах
  mtu 9212
```


## Huawei [path-mtu auto-discovery]
У Хуавея по умолчанию PMTUD [выключен](https://support.huawei.com/enterprise/en/doc/EDOC1100058917/dd638600/configuring-path-mtu-auto-discovery)
```bash
# MTU выставляется непосредственно на интерфейсе или сабинтерфейсе
interface Eth-Trunk19
 mtu 9216

bgp 11111
 peer 2.2.2.2 path-mtu auto-discovery
```
