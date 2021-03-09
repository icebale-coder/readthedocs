title: EVE-NG

# EVE-NG

## Общее

[EVE-FULL](magnet:?xt=urn:btih:b5fc7cd66f0b514a4faaca54c3f6c6c4e3a17508&dn=EVE-FULL&tr=http%3a%2f%2fbt3.t-ru.org%2fann&tr=http%3a%2f%2fretracker.local%2fannounce) - ссылка на торрент с очень большой коллекцией образов для EVE-NG

## IOL

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-iol-ios-on-linux/

1. Скачиваем образа в формате .bin, кидаем в `/opt/unetlab/addons/iol/bin`

2. Исправляем права с помощью

   ```bash
   /opt/unetlab/wrappers/unl_wrapper -a fixpermissions
   ```

3. Создать python-скрипт со следующим содержимым и закинуть его в `/opt/unetlab/addons/iol/bin/`:

   ```python
   #! /usr/bin/python
   print("*********************************************************************")
   print("Cisco IOU License Generator - Kal 2011, python port of 2006 C version")
   print("Modified to work with python3 by c_d 2014")
   import os
   import socket
   import hashlib
   import struct
   
   # get the host id and host name to calculate the hostkey
   hostid=os.popen("hostid").read().strip()
   hostname = socket.gethostname()
   ioukey=int(hostid,16)
   for x in hostname:
    ioukey = ioukey + ord(x)
   print("hostid=" + hostid +", hostname="+ hostname + ", ioukey=" + hex(ioukey)[2:])
   
   # create the license using md5sum
   iouPad1 = b'\x4B\x58\x21\x81\x56\x7B\x0D\xF3\x21\x43\x9B\x7E\xAC\x1D\xE6\x8A'
   iouPad2 = b'\x80' + 39*b'\0'
   md5input=iouPad1 + iouPad2 + struct.pack('!i', ioukey) + iouPad1
   iouLicense=hashlib.md5(md5input).hexdigest()[:16]
   
   print("\nAdd the following text to ~/.iourc:")
   print("[license]\n" + hostname + " = " + iouLicense + ";\n")
   print("You can disable the phone home feature with something like:")
   print(" echo '127.0.0.127 xml.cisco.com' >> /etc/hosts\n")
   ```

4. Запустить этот скрипт `chmod +x iollicense.py && python2 iollicense.py`, его вывод типа

   ```bash
   [license]
   unl01 = 0123456789abcdef;
   ```

5. закинуть в `/opt/unetlab/addons/iol/bin/`.

6. Готово емае

### Использующиеся версии

| .bin                                                         | Версия                                                       | Комментарий |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----------- |
| L2-ADVENTERPRISEK9-M-15.2-20150703.bin                       | Cisco IOS Software, Linux Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Version 15.2(CML_NIGHTLY_20150703)FLO_DSGS7 | Не проверен |
| L2-ADVENTERPRISEK9-M-15.2-IRON-20151103.bin                  | Cisco IOS Software, Linux Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Version 15.2(CML_NIGHTLY_20151103)FLO_DSGS7 | Не проверен |
| L3-ADVENTERPRISEK9-M-15.2-M5.3.bin                           | Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.2(4)M5.3 | Не проверен |
| L3-ADVENTERPRISEK9-M-15.2-S7.bin                             | Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.2(4)S7 | Не проверен |
| L3-ADVENTERPRISEK9-M-15.4-2T.bin                             | Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.4(2)T4 | Не проверен |
| i86bi_linux_l2-advipservicesk9-ms.high_iron_20170202.bin     | Cisco IOS Software, Linux Software (I86BI_LINUXL2-ADVIPSERVICESK9-M), Version 15.2(HI_20170202)FLO_DSGS7 | Не проверен |
| i86bi_linux_l2-ipbasek9-ms.high_iron_aug9_2017b.bin          | Cisco IOS Software, Linux Software (I86BI_LINUXL2-IPBASEK9-M), Experimental Version 15.2(20170809:194209) | Не проверен |
| i86bi_linux_l2-adventerprisek9-ms.SSA.high_iron_20180510.bin | Cisco IOS Software, Linux Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Version 15.2(CML_NIGHTLY_20180510)FLO_DSGS7, | Не проверен |
| i86bi_LinuxL2-AdvEnterpriseK9-M_152_May_2018.bin             | Cisco IOS Software, Linux Software (I86BI_LINUXL2-ADVENTERPRISEK9-M), Version 15.2(CML_NIGHTLY_20180510)FLO_DSGS7 | Не проверен |
| i86bi_LinuxL3-AdvEnterpriseK9-M2_157_3_May_2018.bin          | Cisco IOS Software, Linux Software (I86BI_LINUX-ADVENTERPRISEK9-M), Version 15.7(3)M2 | Не проверен |

## IOS Dynampis

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-dynamips-images-cisco-ios/

1. Загрузить .bin образы в `/opt/unetlab/addons/dynamips`, конвертировать их в .image: `unzip -p c1710-bk9no3r2sy-mz.124-23.bin > c1710-bk9no3r2sy-mz.124-23.image `

2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Готово емае

### Использующиеся версии

| .image                                    | Версия                                                       | Комментарий                                                 |
| ----------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------- |
| c1710-bk9no3r2sy-mz.124-23.image          | Cisco IOS Software, C1700 Software (C1710-BK9NO3R2SY-M), Version 12.4(23), RELEASE SOFTWARE (fc1) | Не проверен                                                 |
| c3725-adventerprisek9-mz.124-15.T14.image | Cisco IOS Software, 3700 Software (C3725-ADVENTERPRISEK9-M), Version 12.4(15)T14, RELEASE SOFTWARE (fc2) | Не проверен                                                 |
| c7200-adventerprisek9-mz.152-4.S7.image   | Cisco IOS Software, 7200 Software (C7200-ADVENTERPRISEK9-M), Version 15.2(4)S7, RELEASE SOFTWARE (fc4) | Не проверен. Возможно расширять маршрутизируемыми модулями. |

## vIOS

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-vios-from-virl/

### Использующиеся версии

| qcow2                                 | Версия                                                       | Комментарий |
| ------------------------------------- | ------------------------------------------------------------ | ----------- |
| vios-adventerprisek9-m-15.6.2T        | Cisco IOS Software, IOSv Software (VIOS-ADVENTERPRISEK9-M), Version 15.6(2)T, RELEASE SOFTWARE (fc2) | Не проверен |
| viosl2-adventerprisek9-m-15.2-2017032 | Cisco IOS Software, vios_l2 Software (vios_l2-ADVENTERPRISEK9-M), Experimental Version 15.2(20170321:233949) [mmen 101] | Не проверен |

## NX-OS 9k

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-nexus-9000v-switch/

Рекомендую искать версию 9.2 и выше, в более старых версиях отсутствовал *какой-то базовый* функционал

1. Загрузить .qcow2 образ в `/opt/unetlab/addons/qemu/nxosv9k-9.2.4` (название папки соотв названию образа)

2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`

3. Зайти на устройство, отменить POAP - при включении появляется сообщение типа 

   ```bash
   Abort Power On Auto Provisioning [yes - continue with normal setup, skip - bypass password and basic configuration, no - continue with Power On Auto Provisioning] (yes/skip/no)[no] yes
   ```
   
   не захотеть применять политику сложности пароля
   
   ```bash
   Do you want to enforce secure password standard (yes/no) [y]: no
   ```
   
   вбить учетные данные для admin, отменить `basic configuration dialog (yes/no): no`

4. В памяти найти образ системы:

   ```bash
   switch# dir bootflash:
          4096    Jan 09 06:45:40 2021  .rpmstore/
          4096    Jan 09 06:46:09 2021  .swtam/
          3490    Jan 09 06:48:08 2021  20210109_064743_poap_31927_init.log
                                        # Вот он
    1339749888    Aug 20 16:21:45 2019  nxos.9.2.4.bin
             0    Jan 09 06:52:35 2021  platform-sdk.cmd
          4096    Jan 09 06:47:36 2021  scripts/
          4096    Jan 09 06:48:47 2021  virt_strg_pool_bf_vdc_1/
          4096    Jan 09 06:46:55 2021  virtual-instance/
            59    Jan 09 06:46:46 2021  virtual-instance.conf
   ```

   задать путь до этого образа для загрузки при старте:

   ```bash
   switch# conf t
   Enter configuration commands, one per line. End with CNTL/Z.
   switch(config)# boot nxos bootflash:nxos.9.2.4.bin
   Performing image verification and compatibility check, please wait....
   switch# copy running-config startup-config
   ```

   При каждом стирании настроек с устройства необходимо повторять процедуру из этого пункта, иначе устройство не сможет найти свой образ системы.

5. Готово емае

### Использующиеся версии

| qcow2         | Версия                                                       | Комментарий                                |
| ------------- | ------------------------------------------------------------ | ------------------------------------------ |
| nxosv9k-9.2.4 | Cisco Nexus Operating System (NX-OS) Software version 9.2(4) | Поверхностная проверка проблем не показала |

## XRv 9k

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-xrv-9000/

1. Скачать образа XRv в .tar.gz или .iso формате, если .tar.gz, то извлечь .iso и закинуть в eve-ng
2. *В случае если система не была установленной см. ссылку*
3. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
4. Стандартные учетные данные `admin/admin`
5. Готово емае

### Использующиеся версии

| qcow2              | Версия                               | Комментарий                                |
| ------------------ | ------------------------------------ | ------------------------------------------ |
| xrv9k-fullk9-6.5.1 | Cisco IOS XR Software, Version 6.5.1 | Поверхностная проверка проблем не показала |

## ASA

Маршрутизируемое устройство. +- аналог 5506.

### Заливка

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Готово емае

### Использующиеся версии

| qcow2              | Версия                                                       | Комментарий                                                  |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| asa-802-k8         |                                                              | Не работает                                                  |
| asa-842-k8         | Cisco Adaptive Security Appliance Software Version 8.4(2) <br/>Device Manager Version 6.4(9) | Маршрутизатор. Лицензия почти бесконечная, anyconnect в комплекте. Не работает OSPF. |
| asa-915-16-k8-CL-L | Cisco Adaptive Security Appliance Software Version 9.1(5)16<br />Device Manager Version 7.3(3) | Маршрутизатор. Лицензия почти бесконечная, anyconnect в комплекте. Не работает OSPF. |

## ASAv

Маршрутизируемое устройство. +- аналог 5506.

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-asav/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем

2. Чтобы присутствовала возможность управлять ASAv через telnet необходимо сделать:

   ```bash
   cd /opt/unetlab/addons/qemu/asav-9101-100/
   apt update
   apt install libguestfs-tools
   
   guestfish -a virtioa.qcow2 
   run # Подождать окончания выполнения, затем
   mount /dev/sda2 /
   touch /use_ttyS0
   umount /
   exit
   ```

3. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`

4. Готово емае

### Использующиеся версии

| qcow2         | Версия                                                       | Комментарий                                                  |
| ------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| asav-991-100  | Cisco Adaptive Security Appliance Software Version 9.9(2) <br/>Firepower Extensible Operating System Version 2.3(1.84)<br/>Device Manager Version 7.9(2) | Лицензия не установлена. Триальные нашёл, но они привязаны к S/N, который у каждой единицы будет разный. |
| asav-971-001  | Cisco Adaptive Security Appliance Software Version 9.7(1) <br/>Firepower Extensible Operating System Version 2.1(1.66)<br/>Device Manager Version 7.7(1) | Лицензия не установлена. Триальные нашёл, но они привязаны к S/N, который у каждой единицы будет разный. |
| asav-9101-100 | Cisco Adaptive Security Appliance Software Version 9.10(1)<br/>Firepower Extensible Operating System Version 2.4(1.103)<br/>Device Manager Version 7.10(1) | Лицензия не установлена. Триальные нашёл, но они привязаны к S/N, который у каждой единицы будет разный. |

## CSR 1000v

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-cisco-csrv1000-16-x-denali-everest-fuji/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Готово емае

### Использующиеся версии

| qcow2                                | Версия                                                       | Комментарий                                |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------ |
| csr1000vng-universalk9.16.09.01.Fuji | Cisco IOS XE Software, Version 16.09.01<br/>Cisco IOS Software [Fuji], Virtual XE Software (X86_64_LINUX_IOSD-UNIVERSALK9-M), Version) | Поверхностная проверка проблем не показала |

## Huawei NE40E

https://forum.huawei.com/enterprise/en/run-ce12800-ne40e-in-eve-ng/thread/653457-861

## Arista vEOS

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-arista-veos/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Стандартные учетные данные - admin без пароля
4. Готово емае

### Использующиеся версии

| qcow2          | Версия                                                       | Комментарий  |
| -------------- | ------------------------------------------------------------ | ------------ |
| veos-4.21.1.1F | Software image version: 4.21.1.1F<br/>Architecture:           i386<br/>Internal build version: 4.21.1.1F-10146868.42111F | Не проверено |

## MikroTik RouterOS

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-mikrotik-cloud-router/

1. Скачать из https://mikrotik.com/download *Cloud Hosted Router - Raw disk image*

2. Распаковать .img файл из архива, загрузить его на eve-ng

3. Создать папку `/opt/unetlab/addons/qemu/mikrotik-X.X.X/`(в соотв с версией RouterOS). Конвертировать .img диск в .qcow2 формат:

   ```bash
   cd /opt/unetlab/addons/qemu/mikrotik-6.48/
   mv chr-6.48.img hda.qcow2
   ```

4. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`

4. Стандартные учетные данные - admin без пароля

5. Готово емае

### Использующиеся версии

| qcow2           | Версия                                 | Комментарий                                                  |
| --------------- | -------------------------------------- | ------------------------------------------------------------ |
| mikrotik-6.40.9 | MikroTik RouterOS 6.40.9 (c) 1999-2018 | Поверхностная проверка проблем не показала. Все версии выше имеют проблему с постоянной перезагрузкой. |

## Juniper vRR

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-juniper-vrr/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Стандартные учетные данные - admin без пароля
4. Готово емае

### Использующиеся версии

| qcow2                | Версия                                                       | Комментарий  |
| -------------------- | ------------------------------------------------------------ | ------------ |
| junipervrr-17.2R1.13 | Software image version: 4.21.1.1F<br/>Architecture:           i386<br/>Internal build version: 4.21.1.1F-10146868.42111F | Не проверено |

## Juniper vMX

vMX состоит из двух составляющих:

* **VCP** (Virtual Control Plane) - выделенный control plane, на нём выполняется большинство "вычислительных" операций, таких как пересчёт таблицы маршрутизации
* **VFP** (Virtual Forwarding Plane) - выделенный forwarding plane, на нём находятся интерфейсы устройства, а также он выполняет функции пересылки пакетов.

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-juniper-vmx-16-x-17-x/

1. Скачать архив с vMX VCP и VFP одинаковых версий
2. Распаковать архив с .qcow2 файлом в папку с соотв именем
3. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
4. При запуске ноды добавить в лабораторию и VCP, и VFP, соединить их через интерфейс `em1 / int`
5. Стандартные учетные данные - root без пароля
6. Готово емае

### Использующиеся версии

### Использующиеся версии

| qcow2                                        | Версия                                                       | Комментарий            |
| -------------------------------------------- | ------------------------------------------------------------ | ---------------------- |
| vmxvcp-18.2R1.9-VCP<br/>vmxvfp-18.2R1.9-vFPC | Model: vmx<br/>Junos: 18.2R1.9<br/>JUNOS OS Kernel 64-bit  [20180614.6c3f819_builder_stable_11] | Запускаются, дальше ХЗ |

## Palo Alto

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-palo-alto/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. По умолчанию eve-ng ставит консолью vnc, нужно сменить на telnet
4. Стандартные учетные данные - admin без пароля
5. Готово емае

### Использующиеся версии

| qcow2                | Версия                                                       | Комментарий  |
| -------------------- | ------------------------------------------------------------ | ------------ |
| junipervrr-17.2R1.13 | Software image version: 4.21.1.1F<br/>Architecture:           i386<br/>Internal build version: 4.21.1.1F-10146868.42111F | Не проверено |

## pfSense Firewall

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-pfsense-fw-2-3/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Готово емае

### Использующиеся версии

| qcow2             | Версия        | Комментарий  |
| ----------------- | ------------- | ------------ |
| pfsense-pfs-2.3.5 | 2.3.5-RELEASE | Не проверено |

## VyOS

### Заливка

https://www.eve-ng.net/index.php/documentation/howtos/howto-add-vyos-vyatta/

1. Распаковать архив с .qcow2 файлом в папку с соотв именем
2. Поправить права `/opt/unetlab/wrappers/unl_wrapper -a fixpermissions`
3. Стандартные учетные - `vyos/vyos`
4. Готово емае

### Использующиеся версии

| qcow2            | Версия        | Комментарий  |
| ---------------- | ------------- | ------------ |
| vyos-1.1.8-amd64 | 2.3.5-RELEASE | Не проверено |

