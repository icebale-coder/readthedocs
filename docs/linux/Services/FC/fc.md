# FC 

## FC диагностика

### Просмотр HBA устройств

<details><summary>lspci -nn | grep -i hba</summary>
<p>

```bash
[root@server1]# lspci -nn | grep -i hba
06:00.0 Fibre Channel [0c04]: QLogic Corp. ISP2432-based 4Gb Fibre Channel to PCI Express HBA [1077:2432] (rev 03)
18:00.0 Fibre Channel [0c04]: QLogic Corp. ISP2432-based 4Gb Fibre Channel to PCI Express HBA [1077:2432] (rev 03)

```
</p>
</details>

### Детальный просмотр информации о HBA
<details><summary>systool -c fc_host -v</summary>

```bash
[root@server1]# systool -c fc_host -v
Class = "fc_host"

  Class Device = "host2"
  Class Device path = "/sys/devices/pci0000:17/0000:17:00.0/0000:18:00.0/host2/fc_host/host2"
    dev_loss_tmo        = "30"
    fabric_name         = "0xffffffffffffffff"
    issue_lip           = <store method only>
    max_npiv_vports     = "127"
    node_name           = "0x2000001b320f5fca"
    npiv_vports_inuse   = "0"
    port_id             = "0x0000ef"
    port_name           = "0x2100001b320f5fca"
    port_state          = "Online"
    port_type           = "LPort (private loop)"
    speed               = "4 Gbit"
    supported_classes   = "Class 3"
    supported_speeds    = "1 Gbit, 2 Gbit, 4 Gbit"
    symbolic_name       = "QLE2460 FW:v8.06.00 DVR:v8.07.00.38.07.4-k1"
    system_hostname     = ""
    tgtid_bind_type     = "wwpn (World Wide Port Name)"
    uevent              =
    vport_create        = <store method only>
    vport_delete        = <store method only>

    Device = "host2"
    Device path = "/sys/devices/pci0000:17/0000:17:00.0/0000:18:00.0/host2"
      fw_dump             =
      nvram               = "ISP "
      optrom_ctl          = <store method only>
      optrom              =
      reset               = <store method only>
      sfp                 = ""
      uevent              = "DEVTYPE=scsi_host"
      vpd                 = "-4"


  Class Device = "host3"
  Class Device path = "/sys/devices/pci0000:05/0000:05:00.0/0000:06:00.0/host3/fc_host/host3"
    dev_loss_tmo        = "30"
    fabric_name         = "0xffffffffffffffff"
    issue_lip           = <store method only>
    max_npiv_vports     = "127"
    node_name           = "0x2000001b320fdac2"
    npiv_vports_inuse   = "0"
    port_id             = "0x000000"
    port_name           = "0x2100001b320fdac2"
    port_state          = "Linkdown"
    port_type           = "Unknown"
    speed               = "unknown"
    supported_classes   = "Class 3"
    supported_speeds    = "1 Gbit, 2 Gbit, 4 Gbit"
    symbolic_name       = "QLE2460 FW:v8.06.00 DVR:v8.07.00.38.07.4-k1"
    system_hostname     = ""
    tgtid_bind_type     = "wwpn (World Wide Port Name)"
    uevent              =
    vport_create        = <store method only>
    vport_delete        = <store method only>

    Device = "host3"
    Device path = "/sys/devices/pci0000:05/0000:05:00.0/0000:06:00.0/host3"
      fw_dump             =
      nvram               = "ISP "
      optrom_ctl          = <store method only>
      optrom              =
      reset               = <store method only>
      sfp                 = ""
      uevent              = "DEVTYPE=scsi_host"
      vpd                 = "-4"

```
</p>
</details>


### Просмотр информации о статусе линков HBA  - бывает полезным для быстрого понимания ситуации

<details><summary>systool -c fc_host -v | grep </summary>
<p>

```bash
[root@server1]# systool -c fc_host -v | grep port_state
    port_state          = "Online"
    port_state          = "Linkdown"
```
</p>
</details>

### Посмотреть список примонтированных устройств в системе

<details><summary>ls -l /dev/disk/by-path/ | grep -v part | grep </summary>
<p>

```bash
[root@server1 ~]# ls -l /dev/disk/by-path/ | grep -v part
total 0
lrwxrwxrwx 1 root root  9 Feb  7 11:49 pci-0000:00:1f.1-ata-1.0 -> ../../sr0
lrwxrwxrwx 1 root root  9 Feb  7 11:49 pci-0000:04:00.0-scsi-0:1:1:0 -> ../../sdc
lrwxrwxrwx 1 root root  9 Mar 27 08:17 pci-0000:18:00.0-fc-0x200500a0b82ab6ae-lun-10 -> ../../sda
lrwxrwxrwx 1 root root  9 Mar 27 08:17 pci-0000:18:00.0-fc-0x200500a0b82ab6ae-lun-11 -> ../../sdb
```

</p>
</details>


