# upgrade MX80

Процесс обновление JunOS Juniper MX80  


##  1. Посмотреть текущую версию ПО
<details><summary>show version</summary>
<p>

```bash
admin@MBR> show version 
	Hostname: MBR

	Model: mx80
	Junos: 15.1R7.8
	JUNOS Base OS boot [15.1R7.8]
	JUNOS Base OS Software Suite [15.1R7.8]
	JUNOS Crypto Software Suite [15.1R7.8]
	JUNOS Packet Forwarding Engine Support (MX80) [15.1R7.8]
	JUNOS Web Management [15.1R7.8]
	JUNOS Online Documentation [15.1R7.8]
	JUNOS Services Application Level Gateways [15.1R7.8]
	JUNOS Services Jflow Container package [15.1R7.8]
	JUNOS Services Stateful Firewall [15.1R7.8]
	JUNOS Services NAT [15.1R7.8]
	JUNOS Services RPM [15.1R7.8]
	JUNOS Services Captive Portal and Content Delivery Container package [15.1R7.8]
	JUNOS Macsec Software Suite [15.1R7.8]
	JUNOS Services Crypto [15.1R7.8]
	JUNOS Services IPSec [15.1R7.8]
	JUNOS Kernel Software Suite [15.1R7.8]
```
</p>
</details>

##  2. Через shell - копируем образ с флешки на диск 
<details><summary>start shell</summary>
<p>

```bash
	"!!!По факту просто попадаешь в shell FreeBSD!!!"
	"!!!тут действует большинство команд FreeBSD!!!"
	admin@MBR> start shell
		%su root
	
	"Просмотр содержимого /var/tmp"
	ls -la /var/tmp
		total 789184
		drwxrwxrwt   7 root   field        512 Feb 15 18:53 .
		drwxr-xr-x  34 root   wheel       1024 Feb 16  2020 ..
		drwxr-xr-x   2 root   field        512 Feb 15 17:23 gres-tp
		drwxrwxrwx   2 root   wheel        512 Jan 24  2019 install
		-rw-r--r--   1 admin  field  403906746 Feb 11 16:34 jinstall-ppc-19.4R3.11-signed.tgz
		drwxrwxrwx   2 root   wheel        512 Feb 22  2018 pics
		-r--r--r--   1 root   field        237 Jan 24  2019 preinstall_boot_loader.conf
		drwxr-xr-x   2 root   field        512 Feb 15 17:23 rtsdb
		drwxrwxrwt   2 root   wheel        512 Jan 24  2019 vi.recover
	
	"Проверяем свободное место"
	df -h
		Filesystem             Size    Used   Avail Capacity  Mounted on
		/dev/da0s1a            885M    226M    588M    28%    /
		devfs                  1.0K    1.0K      0B   100%    /dev
		/dev/md0                63M     63M      0B   100%    /packages/mnt/jbase
		/dev/md1               260M    260M      0B   100%    /packages/mnt/jkernel-ppc-15.1R7.8
		/dev/md2               167M    167M      0B   100%    /packages/mnt/jpfe-MX80-15.1R7.8
		/dev/md3                12M     12M      0B   100%    /packages/mnt/jdocs-15.1R7.8
		/dev/md4               115M    115M      0B   100%    /packages/mnt/jroute-ppc-15.1R7.8
		/dev/md5                29M     29M      0B   100%    /packages/mnt/jcrypto-ppc-15.1R7.8
		/dev/md6               270K    270K      0B   100%    /packages/mnt/jmacsec-15.1R7.8
		/dev/md7                26M     26M      0B   100%    /packages/mnt/jweb-ppc-15.1R7.8
		/dev/md8               2.8G     10K    2.6G     0%    /tmp
		/dev/md9               2.8G    2.3M    2.6G     0%    /mfs
		/dev/da0s1e             98M     34K     90M     0%    /config
		procfs                 4.0K    4.0K      0B   100%    /proc
		/dev/da1s1f            2.8G    437M    2.2G    17%    /var
		/var/jails/rest-api    2.8G    437M    2.2G    17%    /packages/mnt/jroute-ppc-15.1R7.8/web-api/var
		/var/jail              2.8G    437M    2.2G    17%    /packages/mnt/jweb-ppc-15.1R7.8/jail/var
		/var/log               2.8G    437M    2.2G    17%    /packages/mnt/jweb-ppc-15.1R7.8/jail/var/log
		devfs                  1.0K    1.0K      0B   100%    /packages/mnt/jweb-ppc-15.1R7.8/jail/dev

	"Создаем директорию для монтирование флешки"
		mkdir /var/tmp/usb

	"Монтируем флешку" 
		mount -t msdos /dev/da1s1 /var/tmp/usb

	"Копируем ПО в /var/tmp"
		cp /var/tmp/usb/jinstall-ppc-19.4R3.11-signed.tgz
	
	"Размонтируем флешку"
		unmount /var/tmp/usb

	"Возвращаемся в cli Juniper-a"
		cli
```

</p>
</details>


##  4. Очищаем логи - удаляем ненужные файлы, чтобы освободить место
<details><summary>request system storage cleanup</summary>
<p>
</p>
</details>

##  5. Cохраняем состояние системы  до обновления (делаем snapshot системы)
<details><summary>request system snapshot </summary>
<p>
```bash
	request system snapshot 
	Verifying compatibility of destination media partitions...
	Running newfs (899MB) on internal media  / partition (da1s1a)...
	Running newfs (100MB) on internal media  /config partition (da1s1e)...
	Copying '/dev/da0s1a' to '/dev/da1s1a' .. (this may take a few minutes)
	Copying '/dev/da0s1e' to '/dev/da1s1e' .. (this may take a few minutes)
	The following filesystems were archived: / /config
```
</p>
</details>

##  6. Проверяем инсталляционный пакет
<details><summary>request system software validate /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz</summary>
<p>
```bash
admin@MBR> request system software validate /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz
	Checking compatibility with configurationalidate /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz     
	Initializing...
	Using jbase-ppc-15.1R7.8
	Verified manifest signed by PackageProductionEc_2018 method ECDSA256+SHA256
	Using /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz
	Verified jinstall-ppc-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jinstall-ppc-19.4R3.11.tgz
	Using jbundle-ppc-19.4R3.11.tgz
	Checking jbundle-ppc requirements on /
	Using jbase-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jbase-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using /var/v/c/tmp/jbundle-ppc/jboot-ppc-19.4R3.11.tgz
	Using jcrypto-dp-support-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jcrypto-dp-support-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jcrypto-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jcrypto-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jdocs-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jdocs-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jkernel-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jkernel-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jmacsec-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jmacsec-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jpfe-ppc-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-ACX-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-MX104-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-MX80-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jpfe-MX80-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jroute-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jroute-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jsd-powerpc-19.4R3.11-jet-1.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jsd-powerpc-19.4R3.11-jet-1 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jsdn-powerpc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jsdn-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jservices-crypto-ppc-19.4R3.11.tgz
	Using jservices-ppc-19.4R3.11.tgz
	Using jweb-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jweb-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-base-powerpc-19.4R3.11.tgz
	Verified py-base-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-base-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-base2-powerpc-19.4R3.11.tgz
	Verified py-base2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-base2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-extensions-powerpc-19.4R3.11.tgz
	Verified py-extensions-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-extensions-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-extensions2-powerpc-19.4R3.11.tgz
	Verified py-extensions2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-extensions2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Hardware Database regeneration succeeded
	Validating against /config/juniper.conf.gz
	mgd: commit complete
	Validation succeeded
```
</p>
</details>

!!!warning "Важно"
          Бывает ситуация, что сбито время и не валидиуется сертификат, 
					тогда надо руками задать текущее время, чтобы пакет обновления установился успешно


##  7. Устанавливаем новый инсталляционный пакет Junos
<details><summary>request system software add /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz</summary>
<p>

```bash
"Может возникнуть ошибка проверки сертификата из-за неправильного времени в коробке"
admin@MBR> request system software add /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz          
	[Jan 20 13:28:06]: Checking pending install on fpc0

	[Jan 20 13:28:06]: Validating on fpc0
	[Jan 20 13:28:24]: Done with validate on all virtual chassis members

	fpc0:
	Verify the signature of the new package
	verify-sig: cannot validate certs.pem
	certificate is not yet valid: 
	/C=US/ST=CA/L=Sunnyvale/O=Juniper Networks/OU=Juniper 
	CA/CN=PackageProductionSHA1RSACA/emailAddress=ca@juniper.net

	ERROR: Package signature validation failed.  Aborting install.

"Для устранения данной ошибки устанавливаем текущее время в ручную"
admin@MBR> set date 202206011436.00 
  Wed Jun  1 14:36:00 GMT-3 2022

"После этого процесс установки проходит без ошибок"
admin@MBR> request system software add /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz          
	NOTICE: Validating configuration against jinstall-ppc-19.4R3.11-signed.tgz.
	NOTICE: Use the 'no-validate' option to skip this if desired.
	Checking compatibility with configuration
	Initializing...
	Using jbase-ppc-15.1R7.8
	Verified manifest signed by PackageProductionEc_2018 method ECDSA256+SHA256
	Using /var/tmp/jinstall-ppc-19.4R3.11-signed.tgz
	Verified jinstall-ppc-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jinstall-ppc-19.4R3.11.tgz
	Using jbundle-ppc-19.4R3.11.tgz
	Checking jbundle-ppc requirements on /
	Using jbase-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jbase-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using /var/v/c/tmp/jbundle-ppc/jboot-ppc-19.4R3.11.tgz
	Using jcrypto-dp-support-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jcrypto-dp-support-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jcrypto-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jcrypto-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jdocs-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jdocs-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jkernel-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jkernel-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jmacsec-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jmacsec-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jpfe-ppc-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-ACX-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-MX104-19.4R3.11.tgz
	Verified SHA1 checksum of jpfe-MX80-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jpfe-MX80-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jroute-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jroute-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jsd-powerpc-19.4R3.11-jet-1.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jsd-powerpc-19.4R3.11-jet-1 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jsdn-powerpc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jsdn-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using jservices-crypto-ppc-19.4R3.11.tgz
	Using jservices-ppc-19.4R3.11.tgz
	Using jweb-ppc-19.4R3.11.tgz
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified jweb-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-base-powerpc-19.4R3.11.tgz
	Verified py-base-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-base-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-base2-powerpc-19.4R3.11.tgz
	Verified py-base2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-base2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-extensions-powerpc-19.4R3.11.tgz
	Verified py-extensions-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-extensions-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Using py-extensions2-powerpc-19.4R3.11.tgz
	Verified py-extensions2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Verified py-extensions2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Hardware Database regeneration succeeded
	Validating against /config/juniper.conf.gz
	mgd: commit complete
	Validation succeeded
	Installing package '/var/tmp/jinstall-ppc-19.4R3.11-signed.tgz' ...
	Verified jinstall-ppc-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
	Adding jinstall-ppc...

	WARNING:     This package will load JUNOS 19.4R3.11 software.
	WARNING:     It will save JUNOS configuration files, and SSH keys
	WARNING:     (if configured), but erase all other files and information
	WARNING:     stored on this machine.  It will attempt to preserve dumps
	WARNING:     and log files, but this can not be guaranteed.  This is the
	WARNING:     pre-installation stage and all the software is loaded when
	WARNING:     you reboot the system.
	
	Saving the config files ...
	NOTICE: uncommitted changes have been saved in /var/db/config/juniper.conf.pre-install
	Installing the bootstrap installer ...
	
	WARNING:     A REBOOT IS REQUIRED TO LOAD THIS SOFTWARE CORRECTLY. Use the
	WARNING:     'request system reboot' command when software installation is
	WARNING:     complete. To abort the installation, do not reboot your system,
	WARNING:     instead use the 'request system software delete jinstall'
	WARNING:     command as soon as this operation completes.
	
	Saving package file in /var/sw/pkg/jinstall-ppc-19.4R3.11-signed.tgz ...
	Saving state for rollback ...
```
</p>
</details>

##  8. Перезагружаем систему
<details><summary>request system reboot</summary>
<p>
​```bash
request system reboot
   ==================================================================================

		MBR
	 (ttyu0)
	
		login:
		MBR
	 (ttyu0)
	
		login:
		MBR
	 (ttyu0)
	
		login: 
		MBR
	 (ttyu0)
	
		logiadmin
		Password:
	
		--- JUNOS 15.1R7.8 built 2018-04-27 20:48:29 UTC
		admin@MBR
	>                                                                                                                                                                                                                              
		*** FINAL System shutdown message from admin@MBR
	 ***
	
		System going down IMMEDIATELY


		FWaiting (max 60 seconds) for system process `vnlru_mem' to stop...done
		Waiting (max 60 seconds) for system process `vnlru' to stop...done
		Waiting (max 60 seconds) for system process `bufdaemon' to stop...done
		Waiting (max 60 seconds) for system process `syncer' to stop...
		Syncing disks, vnodes remaining...0 0 0 done
	
		syncing disks... All buffers synced.
		Uptime: 365d14h47m17s
		recorded reboot as normal shutdown
		Rebooting...
		I2C:   ready


		U-Boot 1.1.6 (Feb  3 2010 - 11:57:02)
	
		CPU:   8572, Version: 2.1, (0x80e00021)
		Core0:  E500, Version: 3.0, (0x80210030)
		Clock Configuration:
		       CPU0:1333 MHz,        CPU1:1333 MHz, CCB: 533 MHz,
		       DDR: 267 MHz (533 MT/s data rate) (Synchronous), LBC:  33 MHz
		L1:    D-cache 32 kB enabled
		       I-cache 32 kB enabled
		Board: MX80 1.12
		CPLD:  Version 0x1d
		DRAM:  Initializing  -     DDR: 2048 MB
		Testing DRAM from 0x00000000 to 0x80000000
		DRAM test phase 1:
		DRAM test phase 2:
		DRAM test passed.
		Now running in RAM - U-Boot at: 0ffa0000
		Enable CPLD Watchdog
		POST: U-boot memory location PASSED
	
		Scaning PCIE bus:
	
		Scanning PCI Express` Bus .. for bus 0
		    Found(0.0.0), (0x1957,0x41) Class(0xb20) :MPC8572 PCIE Controller
		    BAR0 = 0xc0000000
	
		Scanning PCI Express` Bus .. for bus 1
		    Found(1.0.0), (0x10b5,0x8112) Class(0x604) :PEX8112 PCIe-to-PCI Bridge
		    BAR0 = 0xc0100000    BAR1 = 0x0
	
		Scanning PCI Express` Bus .. for bus 2
		    Found(2.1.0), (0x1033,0x35) Class(0xc03) :uPD720101/2 USB(OHCI) Controller
		    BAR0 = 0xc0200000
		    Found(2.1.1), (0x1033,0xe0) Class(0xc03) :uPD720101/2 USB(EHCI) Controller
		    BAR0 = 0xc0201000
		 ----------- PCIE scan complete, last bus = 2 ------------
	
		FLASH:  8 MB
		L2 cache 1MB: enabled
		In:    serial
		Out:   serial
		Err:   serial
		USB:   scanning bus for devices... 3 USB Device(s) found
		       scanning bus for storage devices... 2 Storage Device(s) found
		Net:   fxp0: PHY is Marvell 88E1112S (1410c97)
		me0, em0, fxp0 [PRIME], em1


		ELF file is 32 bit
		Loading .text @ 0x00010080 (176156 bytes)
		Loading .rodata @ 0x0003b09c (14008 bytes)
		Loading .rodata.str1.4 @ 0x0003e754 (15716 bytes)
		Loading set_Xcommand_set @ 0x000424b8 (88 bytes)
		Loading .rodata.cst4 @ 0x00042510 (12 bytes)
		Loading .data @ 0x00043000 (13896 bytes)
		Loading .sdata @ 0x00046648 (80 bytes)
		Clearing .sbss @ 0x00046698 (264 bytes)
		Clearing .bss @ 0x000467a0 (9232 bytes)
		## Starting application at 0x00010080 ...
		Consoles: U-Boot console
		Will try to boot from
		USB
		nand-flash0
		nand-flash1
	
		FreeBSD/PowerPC U-Boot bootstrap loader, Revision 2.2
		(vaidyasd@svl-junos-pool69.juniper.net, Wed Feb  3 09:50:07 PST 2010)
		Memory: 2048MB
		Trying to boot from nand-flash0
		Loading /boot/defaults/loader.conf
		/boot/installer text=0x85f194 data=0x58ec8+0xac700 syms=[0x4+0x7a680+0x4+0xbed0a]
		|
		Hit [Enter] to boot immediately, or space bar for command prompt.
		Booting [/boot/installer]...
		Kernel entry at 0xa00000e0 ...
		GDB: debug ports: uart
		GDB: current port: uart
		KDB: debugger backends: ddb gdb
		KDB: current backend: ddb
		platform_early_bootinit: MX-PPC Series Early Boot Initialization
		mxppc_set_re_type: hw.board.type is MX80
		mxppc_set_re_type: REtype:78, model:mx80, model:MX80, i2cid:2447
		WDOG initialized
		Copyright (c) 1996-2020, Juniper Networks, Inc.
		All rights reserved.
		Copyright (c) 1992-2007 The FreeBSD Project.
		Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
		        The Regents of the University of California. All rights reserved.
		FreeBSD is a registered trademark of The FreeBSD Foundation.
		JUNOS 19.4R3.11 #0: 2020-10-08 21:55:46 UTC
		    builder@qnc-jre-emake1t.juniper.net:/volume/build/junos/19.4/release/19.4R3.11/obj/powerpc/junos/bsd/kernels/MFS-PPC/kernel
		Timecounter "decrementer" frequency 66666666 Hz quality 0
		cpu0: Freescale e500v2 core revision 3.0
		cpu0: HID0 80004000<EMCP,TBEN>
		real memory  = 2084569088 (1988 MB)
		avail memory = 2044055552 (1949 MB)
		netisr_init: forcing maxthreads from 4 to 1
		ETHERNET SOCKET BRIDGE initialising
		Initializing M/T/EX platform properties ..
		nexus0: <Powerpc Nexus device>
		ocpbus0: <on-chip peripheral bus> on nexus0
		openpic0: <OpenPIC in on-chip peripheral bus> iomem 0xf7f40000-0xf7f600b3 on ocpbus0
		uart0: <16550 or compatible> iomem 0xf7f04500-0xf7f0450f irq 58 on ocpbus0
		uart0: console (9600,n,8,1)
		uart1: <16550 or compatible> iomem 0xf7f04600-0xf7f0460f irq 58 on ocpbus0
		lbc0: <Freescale Local Bus Controller> iomem 0xf7f05000-0xf7f05fff,0xf8000000-0xffffffff irq 20,21,22,24 on ocpbus0
		cfi0: <AMD/Fujitsu - 8MB> iomem 0xff800000-0xffffffff on lbc0
		tbbcpld0 iomem 0xff700000-0xff7fffff on lbc0
		tbbcpld_attach: 1st IRQ alloc; start:4 end:4 flags:7
		tbbcpld_attach: 2st IRQ alloc; start:6 end:6 flags:7
		uart2: <16750 or compatible> iomem 0xff600000-0xff600007 on lbc0
		i2c0: <MPC85XX OnChip i2c Controller> iomem 0xf7f03000-0xf7f03014 irq 59 on ocpbus0
		ds1672 rtc0: <DS1672 RTC> on i2c0
		i2c1: <MPC85XX OnChip i2c Controller> iomem 0xf7f03100-0xf7f03114 irq 59 on ocpbus0
		tsec0: <eTSEC ethernet controller> iomem 0xf7f24000-0xf7f24fff irq 45,46,50 on ocpbus0
		tsec0: hardware MAC address 02:00:00:00:00:0b
		miibus0: <MII bus> on tsec0
		gentbi0: <Generic ten-bit interface> on miibus0
		gentbi0:  1000baseSX-FDX, 1000baseT-FDX, auto
		tsec1: <eTSEC ethernet controller> iomem 0xf7f25000-0xf7f25fff irq 51,52,56 on ocpbus0
		tsec1: hardware MAC address 02:00:00:00:00:04
		miibus1: <MII bus> on tsec1
		gentbi1: <Generic ten-bit interface> on miibus1
		gentbi1:  1000baseSX-FDX, 1000baseT-FDX, auto
		tsec2: <eTSEC ethernet controller> iomem 0xf7f26000-0xf7f26fff irq 47,48,49 on ocpbus0
		tsec2: hardware MAC address 5c:5e:ab:09:61:ff
		miibus2: <MII bus> on tsec2
		e1000phy0: <Marvell 88E1112 Gigabit PHY> on miibus2
		e1000phy0:  10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, 1000baseTX-FDX, auto
		tsec3: <eTSEC ethernet controller> iomem 0xf7f27000-0xf7f27fff irq 53,54,55 on ocpbus0
		tsec3: hardware MAC address 02:00:02:00:00:04
		miibus3: <MII bus> on tsec3
		gentbi2: <Generic ten-bit interface> on miibus3
		gentbi2:  1000baseSX-FDX, 1000baseT-FDX, auto
		pcib0: <Freescale MPC8572 PCI Express host controller> iomem 0xf7f09000-0xf7f09fff,0xe8000000-0xebffffff on ocpbus0
		pci0: <PCI bus> on pcib0
		pcib1: <PCI-PCI bridge> mem 0xe8000000-0xe80fffff at device 0.0 on pci0
		pci1: <PCI bus> on pcib1
		pcib2: <PCI-PCI bridge> mem 0xe8100000-0xe810ffff irq 0 at device 0.0 on pci1
		pci2: <PCI bus> on pcib2
		pci2: <serial bus, USB> at device 1.0 (no driver attached)
		ehci0: <NEC uPD 72010x USB 2.0 controller> mem 0xe8200000-0xe82000ff irq 21 at device 1.1 on pci2
		usb0: EHCI version 1.0
		usb0: <NEC uPD 72010x USB 2.0 controller> on ehci0
		usb0: USB revision 2.0
		uhub0: NEC EHCI root hub, class 9/0, rev 2.00/1.00, addr 1
		uhub0: 3 ports with 3 removable, self powered
		umass0: ATP Electronics ATP IG eUSB SSD, rev 2.00/11.00, addr 2
		umass1: ATP Electronics ATP IG eUSB SSD, rev 2.00/11.00, addr 3
		Initializing product: 88 ..
		Initializing MX-PPC platform mastership..
		Registering tcp_platform_dependent = tcp_handle_special_ports
		md0: Preloaded image </boot/modules/mdimg> 32723456 bytes at 0xa0a9e95c
		da0 at umass-sim0 bus 0 target 0 lun 0
		da0: <ATP ATP IG eUSB SSD 1100> Fixed Direct Access SCSI-0 device
		da0: 40.000MB/s transfers
		da0: 3920MB (8028160 512 byte sectors: 255H 63S/T 499C)
		da1 at umass-sim1 bus 1 target 0 lun 0
		da1: <ATP ATP IG eUSB SSD 1100> Fixed Direct Access SCSI-0 device
		da1: 40.000MB/s transfers
		da1: 3920MB (8028160 512 byte sectors: 255H 63S/T 499C)
		Kernel thread "wkupdaemon" (pid 52) exited prematurely.
		Trying to mount root from cd9660:/dev/md0
		Disabling watchdog
		=================== Bootstrap installer starting ===================
		Initialized the environment
		Routing engine model is RE-MX80
		HW model is Freescale e500v2 core
		Discovered that flash disk = da0 , hard disk = da1
		** /dev/da0s1a
		** Last Mounted on /mnt
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		1877 files, 126380 used, 326560 free (56 frags, 40813 blocks, 0.0% fragmentation)
		** /dev/da0s1e
		** Last Mounted on /config
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		9 files, 17 used, 50264 free (16 frags, 6281 blocks, 0.0% fragmentation)
		** /dev/da1s1a
		** Last Mounted on /tmp/.snp5531/mnt
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		1862 files, 115856 used, 337336 free (16 frags, 42165 blocks, 0.0% fragmentation)
		** /dev/da1s1e
		** Last Mounted on /tmp/.snp5531/mnt
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		9 files, 13 used, 50488 free (16 frags, 6309 blocks, 0.0% fragmentation)
		** /dev/da1s1f
		** Last Mounted on /var
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		582 files, 622519 used, 845708 free (788 frags, 105615 blocks, 0.1% fragmentation)
		Disk to install is da0
		mfs: available=3921376
		hw.physmem: 2139095040
		hw.usermem: 2073767936
		hw.realmem: 2084569088
		Using 3921376 for /tmp
		Setting ospackage=jboot-ppc-19.4R3.11.tgz, configpackage=configs-19.4R3.11.tgz
		Setting packlist=jbundle-ppc-19.4R3.11.tgz
		Partitioning da0 ...
		******* Working on device /dev/da0 *******
		Installing disk label on da0s1
		Running newfs on da0s1a...
		/dev/da0s1a: 899.2MB (1841556 sectors) block size 16384, fragment size 2048
		        using 5 cylinder groups of 183.62MB, 11752 blks, 23552 inodes.
		super-block backups (for fsck -b #) at:
		 32, 376096, 752160, 1128224, 1504288
		Running newfs on da0s1e...
		/dev/da0s1e: 99.9MB (204616 sectors) block size 16384, fragment size 2048
		        using 4 cylinder groups of 24.98MB, 1599 blks, 3200 inodes.
		super-block backups (for fsck -b #) at:
		 32, 51200, 102368, 153536
		Clearing the last sector of da0s1a...
		Clearing the last sector of da0s1e...
		chflags: not found
		Installing JUNOS on da0...
		Adding jbase...
		Mounted jbase on /mnt/packages/mnt/jbase (/dev/md2)
		Restoring backed up configurations...
		Adding jbundle-ppc-19.4R3.11.tgz...
		Checking package integrity...
		Running requirements check first for jbundle-ppc-19.4R3.11...
		Running pre-install for jbundle-ppc-19.4R3.11...
		Installing jbundle-ppc-19.4R3.11 in /var/tmp/jbundle-ppc-19.4R3.11.tgz.1...
		Running post-install for jbundle-ppc-19.4R3.11...
		Verified SHA1 checksum of jbase-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jboot-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jcrypto-dp-support-19.4R3.11.tgz
		Verified SHA1 checksum of jcrypto-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jdocs-19.4R3.11.tgz
		Verified SHA1 checksum of jkernel-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jmacsec-19.4R3.11.tgz
		Verified SHA1 checksum of jpfe-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jroute-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jsd-powerpc-19.4R3.11-jet-1.tgz
		Verified SHA1 checksum of jsdn-powerpc-19.4R3.11.tgz
		Verified SHA1 checksum of jservices-crypto-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jservices-ppc-19.4R3.11.tgz
		Verified SHA1 checksum of jweb-ppc-19.4R3.11.tgz
		Adding jcrypto-ppc...
		Adding jpfe-ppc...
		Adding jweb-ppc...
		Adding jdocs...
		Adding jsdn-powerpc...
		Adding jservices-ppc...
		Installing new jservices-alg ...
		Verified jservices-alg-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-alg-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-alg ...
		Storing jservices-alg-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-alg/jservices-alg-xlp64 -> /var/sw/pkg/jservices-alg-xlp64-19.4R3.11.tgz...
		Installing new jservices-cos ...
		Verified jservices-cos-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-cos-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-cos ...
		Storing jservices-cos-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-cos/jservices-cos-xlp64 -> /var/sw/pkg/jservices-cos-xlp64-19.4R3.11.tgz...
		Installing new jservices-jflow ...
		Verified jservices-jflow-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-jflow-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-jflow ...
		Storing jservices-jflow-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-jflow/jservices-jflow-xlp64 -> /var/sw/pkg/jservices-jflow-xlp64-19.4R3.11.tgz...
		Installing new jservices-sfw ...
		Verified jservices-sfw-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-sfw-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-sfw ...
		Storing jservices-sfw-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-sfw/jservices-sfw-xlp64 -> /var/sw/pkg/jservices-sfw-xlp64-19.4R3.11.tgz...
		Installing new jservices-nat ...
		Verified jservices-nat-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-nat-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-nat ...
		Storing jservices-nat-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-nat/jservices-nat-xlp64 -> /var/sw/pkg/jservices-nat-xlp64-19.4R3.11.tgz...
		Installing new jservices-rpm ...
		Verified jservices-rpm-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-rpm-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-rpm ...
		Storing jservices-rpm-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-rpm/jservices-rpm-xlp64 -> /var/sw/pkg/jservices-rpm-xlp64-19.4R3.11.tgz...
		Installing new jservices-softwire ...
		Verified jservices-softwire-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-softwire-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-softwire ...
		Storing jservices-softwire-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-softwire/jservices-softwire-xlp64 -> /var/sw/pkg/jservices-softwire-xlp64-19.4R3.11.tgz...
		Installing new jservices-cpcd ...
		Verified jservices-cpcd-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-cpcd-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-cpcd ...
		Storing jservices-cpcd-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-cpcd/jservices-cpcd-xlp64 -> /var/sw/pkg/jservices-cpcd-xlp64-19.4R3.11.tgz...
		Adding jmacsec...
		Adding jservices-crypto-ppc...
		Installing new jservices-crypto-base ...
		Verified jservices-crypto-base-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-crypto-base-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-crypto-base ...
		Storing jservices-crypto-base-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-crypto-base/jservices-crypto-base-xlp64 -> /var/sw/pkg/jservices-crypto-base-xlp64-19.4R3.11.tgz...
		Installing new jservices-ipsec ...
		Verified jservices-ipsec-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-ipsec-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-ipsec ...
		Storing jservices-ipsec-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-ipsec/jservices-ipsec-xlp64 -> /var/sw/pkg/jservices-ipsec-xlp64-19.4R3.11.tgz...
		Installing new jservices-rtcom ...
		Verified jservices-rtcom-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-rtcom-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-rtcom ...
		Storing jservices-rtcom-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-rtcom/jservices-rtcom-xlp64 -> /var/sw/pkg/jservices-rtcom-xlp64-19.4R3.11.tgz...
		Installing new jservices-ssl ...
		Verified jservices-ssl-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-ssl-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-ssl ...
		Storing jservices-ssl-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-ssl/jservices-ssl-xlp64 -> /var/sw/pkg/jservices-ssl-xlp64-19.4R3.11.tgz...
		Installing new jservices-tcp-log ...
		Verified jservices-tcp-log-xlp64-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jservices-tcp-log-19.4R3.11.tgz signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Creating /opt/sdk/service-packages/jservices-tcp-log ...
		Storing jservices-tcp-log-xlp64-19.4R3.11.tgz in /var/sw/pkg ...
		Link: /opt/sdk/service-packages/jservices-tcp-log/jservices-tcp-log-xlp64 -> /var/sw/pkg/jservices-tcp-log-xlp64-19.4R3.11.tgz...
		Adding jcrypto-dp-support...
		Adding py-base-powerpc...
		Adding py-base2-powerpc...
		Adding py-extensions-powerpc...
		Adding py-extensions2-powerpc...
		Adding jsd-powerpc-19.4R3.11-jet...
		Adding jkernel-ppc...
		Adding jroute-ppc...
		Unmounted /mnt/packages/mnt/jbase
		machdep.bootsuccess: 0 -> 1
		machdep.nextbootdev: usb -> nand-flash0
		Waiting (max 60 seconds) for system process `vnlru' to stop...done
		Waiting (max 60 seconds) for system process `vnlru_mem' to stop...done
		Waiting (max 60 seconds) for system process `bufdaemon' to stop...done
		Waiting (max 60 seconds) for system process `syncer' to stop...
		Syncing disks, vnodes remaining...0 0 done
	
		syncing disks... All buffers synced.
		Uptime: 11m59s
		Rebooting...
		I2C:   ready


		U-Boot 1.1.6 (Feb  3 2010 - 11:57:02)
	
		CPU:   8572, Version: 2.1, (0x80e00021)
		Core0:  E500, Version: 3.0, (0x80210030)
		Clock Configuration:
		       CPU0:1333 MHz,        CPU1:1333 MHz, CCB: 533 MHz,
		       DDR: 267 MHz (533 MT/s data rate) (Synchronous), LBC:  33 MHz
		L1:    D-cache 32 kB enabled
		       I-cache 32 kB enabled
		Board: MX80 1.12
		CPLD:  Version 0x1d
		DRAM:  Initializing  -     DDR: 2048 MB
		Testing DRAM from 0x00000000 to 0x80000000
		DRAM test phase 1:
		DRAM test phase 2:
		DRAM test passed.
		Now running in RAM - U-Boot at: 0ffa0000
		Enable CPLD Watchdog
		POST: U-boot memory location PASSED
	
		Scaning PCIE bus:
	
		Scanning PCI Express` Bus .. for bus 0
		    Found(0.0.0), (0x1957,0x41) Class(0xb20) :MPC8572 PCIE Controller
		    BAR0 = 0xc0000000
	
		Scanning PCI Express` Bus .. for bus 1
		    Found(1.0.0), (0x10b5,0x8112) Class(0x604) :PEX8112 PCIe-to-PCI Bridge
		    BAR0 = 0xc0100000    BAR1 = 0x0
	
		Scanning PCI Express` Bus .. for bus 2
		    Found(2.1.0), (0x1033,0x35) Class(0xc03) :uPD720101/2 USB(OHCI) Controller
		    BAR0 = 0xc0200000
		    Found(2.1.1), (0x1033,0xe0) Class(0xc03) :uPD720101/2 USB(EHCI) Controller
		    BAR0 = 0xc0201000
		 ----------- PCIE scan complete, last bus = 2 ------------
	
		FLASH:  8 MB
		L2 cache 1MB: enabled
		In:    serial
		Out:   serial
		Err:   serial
		USB:   scanning bus for devices... 3 USB Device(s) found
		       scanning bus for storage devices... 2 Storage Device(s) found
		Net:   fxp0: PHY is Marvell 88E1112S (1410c97)
		me0, em0, fxp0 [PRIME], em1


		ELF file is 32 bit
		Loading .text @ 0x00010080 (176156 bytes)
		Loading .rodata @ 0x0003b09c (14008 bytes)
		Loading .rodata.str1.4 @ 0x0003e754 (15716 bytes)
		Loading set_Xcommand_set @ 0x000424b8 (88 bytes)
		Loading .rodata.cst4 @ 0x00042510 (12 bytes)
		Loading .data @ 0x00043000 (13896 bytes)
		Loading .sdata @ 0x00046648 (80 bytes)
		Clearing .sbss @ 0x00046698 (264 bytes)
		Clearing .bss @ 0x000467a0 (9232 bytes)
		## Starting application at 0x00010080 ...
		Consoles: U-Boot console
		Will try to boot from
		USB
		nand-flash0
		nand-flash1
	
		FreeBSD/PowerPC U-Boot bootstrap loader, Revision 2.2
		(vaidyasd@svl-junos-pool69.juniper.net, Wed Feb  3 09:50:07 PST 2010)
		Memory: 2048MB
		Trying to boot from nand-flash0
		/boot/init.4th loaded.
		Loading /boot/defaults/loader.conf
		/kernel data=0xeb1000+0x1149d4 syms=[0x4+0xb39a0+0x4+0x1105d3]




		Hit [Enter] to boot immediately, or space bar for command prompt.
		Booting [/kernel]...
		Kernel entry at 0xa00000c0 ...
		GDB: debug ports: uart
		GDB: current port: uart
		KDB: debugger backends: ddb gdb
		KDB: current backend: ddb
		platform_early_bootinit: MX-PPC Series Early Boot Initialization
		mxppc_set_re_type: hw.board.type is MX80
		mxppc_set_re_type: REtype:78, model:mx80, model:MX80, i2cid:2447
		WDOG initialized
		Copyright (c) 1996-2020, Juniper Networks, Inc.
		All rights reserved.
		Copyright (c) 1992-2007 The FreeBSD Project.
		Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
		        The Regents of the University of California. All rights reserved.
		FreeBSD is a registered trademark of The FreeBSD Foundation.
		JUNOS 19.4R3.11 #0: 2020-10-08 21:58:24 UTC
		    builder@qnc-jre-emake1t.juniper.net:/volume/build/junos/19.4/release/19.4R3.11/obj/powerpc/junos/bsd/kernels/JUNIPER-PPC/kernel
		WARNING: debug.mpsafenet forced to 0 as ipsec requires Giant
		Timecounter "decrementer" frequency 66666666 Hz quality 0
		cpu0: Freescale e500v2 core revision 3.0
		cpu0: HID0 80004000<EMCP,TBEN>
		real memory  = 2116026368 (2018 MB)
		avail memory = 2075127808 (1978 MB)
		Security policy loaded: JUNOS MAC/pcap (mac_pcap)
		Security policy loaded: JUNOS MAC/runasnonroot (mac_runasnonroot)
		Security policy loaded: Junos MAC/veriexec (mac_veriexec)
		MAC/veriexec fingerprint module loaded: SHA256
		MAC/veriexec fingerprint module loaded: SHA1
		netisr_init: forcing maxthreads from 4 to 1
		random: <Software, Yarrow> initialized
		Initializing M/T/EX platform properties ..
		ETHERNET SOCKET BRIDGE initialising
		nexus0: <Powerpc Nexus device>
		ocpbus0: <on-chip peripheral bus> on nexus0
		openpic0: <OpenPIC in on-chip peripheral bus> iomem 0xf7f40000-0xf7f600b3 on ocpbus0
		uart0: <16550 or compatible> iomem 0xf7f04500-0xf7f0450f irq 58 on ocpbus0
		uart0: console (9600,n,8,1)
		uart1: <16550 or compatible> iomem 0xf7f04600-0xf7f0460f irq 58 on ocpbus0
		lbc0: <Freescale Local Bus Controller> iomem 0xf7f05000-0xf7f05fff,0xf8000000-0xffffffff irq 20,21,22,24 on ocpbus0
		cfi0: <AMD/Fujitsu - 8MB> iomem 0xff800000-0xffffffff on lbc0
		tbbcpld0 iomem 0xff700000-0xff7fffff on lbc0
		tbbcpld_attach: 1st IRQ alloc; start:4 end:4 flags:7
		tbbcpld_attach: 2st IRQ alloc; start:6 end:6 flags:7
		uart2: <16750 or compatible> iomem 0xff600000-0xff600007 on lbc0
		i2c0: <MPC85XX OnChip i2c Controller> iomem 0xf7f03000-0xf7f03014 irq 59 on ocpbus0
		ds1672 rtc0: <DS1672 RTC> on i2c0
		i2c1: <MPC85XX OnChip i2c Controller> iomem 0xf7f03100-0xf7f03114 irq 59 on ocpbus0
		tsec0: <eTSEC ethernet controller> iomem 0xf7f24000-0xf7f24fff irq 45,46,50 on ocpbus0
		tsec0: hardware MAC address 02:00:00:00:00:0b
		miibus0: <MII bus> on tsec0
		gentbi0: <Generic ten-bit interface> on miibus0
		gentbi0:  1000baseSX-FDX, 1000baseT-FDX, auto
		tsec1: <eTSEC ethernet controller> iomem 0xf7f25000-0xf7f25fff irq 51,52,56 on ocpbus0
		tsec1: hardware MAC address 02:00:00:00:00:04
		miibus1: <MII bus> on tsec1
		gentbi1: <Generic ten-bit interface> on miibus1
		gentbi1:  1000baseSX-FDX, 1000baseT-FDX, auto
		tsec2: <eTSEC ethernet controller> iomem 0xf7f26000-0xf7f26fff irq 47,48,49 on ocpbus0
		tsec2: hardware MAC address 5c:5e:ab:09:61:ff
		miibus2: <MII bus> on tsec2
		e1000phy0: <Marvell 88E1112 Gigabit PHY> on miibus2
		e1000phy0:  10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, 1000baseTX-FDX, auto
		tsec3: <eTSEC ethernet controller> iomem 0xf7f27000-0xf7f27fff irq 53,54,55 on ocpbus0
		tsec3: hardware MAC address 02:00:02:00:00:04
		miibus3: <MII bus> on tsec3
		gentbi2: <Generic ten-bit interface> on miibus3
		gentbi2:  1000baseSX-FDX, 1000baseT-FDX, auto
		pcib0: <Freescale MPC8572 PCI Express host controller> iomem 0xf7f09000-0xf7f09fff,0xe8000000-0xebffffff on ocpbus0
		pci0: <PCI bus> on pcib0
		pcib1: <PCI-PCI bridge> mem 0xe8000000-0xe80fffff at device 0.0 on pci0
		pci1: <PCI bus> on pcib1
		pcib2: <PCI-PCI bridge> mem 0xe8100000-0xe810ffff irq 0 at device 0.0 on pci1
		pci2: <PCI bus> on pcib2
		pci2: <serial bus, USB> at device 1.0 (no driver attached)
		ehci0: <NEC uPD 72010x USB 2.0 controller> mem 0xe8200000-0xe82000ff irq 21 at device 1.1 on pci2
		usb0: EHCI version 1.0
		usb0: <NEC uPD 72010x USB 2.0 controller> on ehci0
		usb0: USB revision 2.0
		uhub0: NEC EHCI root hub, class 9/0, rev 2.00/1.00, addr 1
		uhub0: 3 ports with 3 removable, self powered
		umass0: ATP Electronics ATP IG eUSB SSD, rev 2.00/11.00, addr 2
		umass0: SCSI over Bulk-Only; quirks = 0x0000
		umass0:0:0:-1: Attached to scbus0
		umass1: ATP Electronics ATP IG eUSB SSD, rev 2.00/11.00, addr 3
		umass1: SCSI over Bulk-Only; quirks = 0x0000
		umass1:1:1:-1: Attached to scbus1
		Initializing product: 88 ..
		Setting up M/T interface operations and attributes
		Initializing MX-PPC platform mastership..
		Registering tcp_platform_dependent = tcp_handle_special_ports
		da1 at umass-sim1 bus 1 target 0 lun 0
		da1: <ATP ATP IG eUSB SSD 1100> Fixed Direct Access SCSI-0 device
		da1: 40.000MB/s transfers
		da1: 3920MB (8028160 512 byte sectors: 255H 63S/T 499C)
		da0 at umass-sim0 bus 0 target 0 lun 0
		da0: <ATP ATP IG eUSB SSD 1100> Fixed Direct Access SCSI-0 device
		da0: 40.000MB/s transfers
		da0: 3920MB (8028160 512 byte sectors: 255H 63S/T 499C)
		random: unblocking device.
		Kernel thread "wkupdaemon" (pid 63) exited prematurely.
		Trying to mount root from ufs:/dev/da0s1a
		Attaching /packages/jbase via /dev/mdctl...
		Mounted jbase package on /dev/md0...
	
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jboot signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jbase-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jkernel package on /dev/md1...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jkernel-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jpfe package on /dev/md2...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jpfe-MX80-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jdocs package on /dev/md3...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jdocs-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jroute package on /dev/md4...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jroute-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Executing /packages/mnt/jroute-ppc-19.4R3.11/mount.post..
		Mounted jcrypto package on /dev/md5...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jcrypto-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jcrypto-dp-support package on /dev/md6...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jcrypto-dp-support-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jmacsec package on /dev/md7...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jmacsec-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jsd package on /dev/md8...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jsd-powerpc-19.4R3.11-jet-1 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jsdn-powerpc package on /dev/md9...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jsdn-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted jweb package on /dev/md10...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified jweb-ppc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Executing /packages/mnt/jweb-ppc-19.4R3.11/mount.post..
		Mounted py-base-powerpc package on /dev/md11...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified py-base-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted py-base2-powerpc package on /dev/md12...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified py-base2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted py-extensions-powerpc package on /dev/md13...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified py-extensions-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Mounted py-extensions2-powerpc package on /dev/md14...
		Verified manifest signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		Verified py-extensions2-powerpc-19.4R3.11 signed by PackageProductionECP256_2020 method ECDSA256+SHA256
		swapon: adding /dev/da0s1b as swap device
		Automatic reboot in progress...
		** Last Mounted on /mnt
		** Root file system
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		6380 files, 162390 used, 290550 free (38 frags, 36314 blocks, 0.0% fragmentation)
		** Last Mounted on /mnt/config
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		9 files, 17 used, 50264 free (16 frags, 6281 blocks, 0.0% fragmentation)
		** Last Mounted on /mnt/var
		** Phase 1 - Check Blocks and Sizes
		** Phase 2 - Check Pathnames
		** Phase 3 - Check Connectivity
		** Phase 4 - Check Reference Counts
		** Phase 5 - Check Cyl groups
		610 files, 613556 used, 854671 free (783 frags, 106736 blocks, 0.1% fragmentation)
		** /dev/da1s1f
		FILE SYSTEM CLEAN; SKIPPING CHECKS
		clean, 854671 free (783 frags, 106736 blocks, 0.1% fragmentation)
		tunefs: soft updates remains unchanged as disabled
		hw.re.gres_sync_other: 0 -> 1
		Creating initial configuration...mgd: commit complete
		Setting initial options:  debugger_on_panic=NO debugger_on_break=NO.
		Starting optional daemons: .
		Doing initial network setup: keyadmin.
		Initial interface configuration:
		additional daemons:.
		checking for core dump...
		savecore: Router rebooting after a normal shutdown...
		savecore: Router rebooting after a normal shutdown...
		savecore: no dumps found
		Set Enhanced BBE Default...
		Enhanced BBE Default for mx80 set to... 2
		Enhanced arp scale is disabled
		Set Inline Services Cookie Mode to DefauLoading the IMA Link Media Layer; Attaching to media services layer
		Loading the IMA Group Media Layer; Attaching to media services layer
		lt...
		Inline Services Coookie MODE  Default forLoading the SONET Media Layer; Attaching to media services layer
		 mx80 set to... 2
		Additional routing options:kern.module_path: /boot//kernel;/bLoading the CHMIC module
		oot/modules -> /modules/peertype;/modules/ifpfe_drv;/modules/ifpfe_media;/modules/platform;/modules;
		kld netpfe media: ifpfem_ds0 ifpfem_ds1e1 ifpfem_ds3e3 ifpfem_ima ifpfem_otn ifpfem_sonetkld netpfe drv: ifpfed_atm ifpfedLoading POS driver
		_chmic ifpfed_controller ifpfed_ds0 ifpfed_ds1e1 ifpfed_ds3e3 if Loading Aggregate sonet driver
		pfed_ep ifpfed_irb ifpfed_lt ifpfed_ml_cmn ifpfed_ml_ha ifpfed_pos ifpfed_pppoe ifpfed_ps ifpfed_sa ifpfed_svcs Loading Multilink Services PICs module.
		Loading the M&T Platform NETPFE module
		ifpfed_vtkld platform: mt_ifpfekld peertype: peertype_asp peertype_msp peertype_pex peertype_slavere peertype_xdpc pvid_db kld ipsec kldcryptosoft0: <software crypto> on nexus0
		 kats kldIPsec: Initialized Security Association Processing.
		.
		Doing additional network setup: ntpdate.
		Starting final network daemons:.Loading JUNOS chassis module
		chassis_init_hw_chassis_startup_time: chassis startup time 0.000000
	
		 chassis.ko loaded setting ldconfig path: /usr/lib /opt/lib
		starting standard daemons: cron.
		Initial rc.powerpc initialization:.
	
		 Lock Manager
		RDM Embedded 7 [04-Aug-2006] http://www.birdstep.com
		Copyright (c) 1992-2006 Birdstep Technology, Inc.  All Rights Reserved.
	
		Unix Domain sockets Lock manager
		Lock manager 'lockmgr' started successfully.
	
		Database Initialization Utility
		RDM Embedded 7 [04-Aug-2006] http://www.birdstep.com
		Copyright (c) 1992-2006 Birdstep Technology, Inc.  All Rights Reserved.
	
		Profile database initialized
		Local package initialization:.
		kern.securelevel: -1 -> 1
		starting local daemons:set cores for group access
		.
		Tue Feb 16 11:04:40 GMT-7 2021
	
		MBR
	 (ttyu0)
	
		login: Feb 16 11:04:57 init: license-service (PID 2112) sending signal hup: due to "proto-mastership": 0x1
```

</p>
</details>

##  9. Cохраняем состояние системы  после обновления (делаем snapshot системы)
<details><summary>request system snapshot </summary>
<p>

```bash
   request system snapshot 
	Verifying compatibility of destination media partitions...
	Running newfs (899MB) on internal media  / partition (da1s1a)...
	Running newfs (100MB) on internal media  /config partition (da1s1e)...
	Copying '/dev/da0s1a' to '/dev/da1s1a' .. (this may take a few minutes)
	Copying '/dev/da0s1e' to '/dev/da1s1e' .. (this may take a few minutes)
	The following filesystems were archived: / /config
```

</p>
</details>

## 10.  Посмотреть новую версию ПО
<details><summary>show version </summary>
<p>

```bash
	"show version"
		Hostname: MBR
		Model: mx80
		Junos: 19.4R3.11
		JUNOS Base OS boot [19.4R3.11]
		JUNOS Base OS Software Suite [19.4R3.11]
		JUNOS Crypto Software Suite [19.4R3.11]
		JUNOS Packet Forwarding Engine Support (MX80) [19.4R3.11]
		JUNOS Web Management [19.4R3.11]
		JUNOS Online Documentation [19.4R3.11]
		JUNOS SDN Software Suite [19.4R3.11]
		JUNOS Services Application Level Gateways [19.4R3.11]
		JUNOS Services COS [19.4R3.11]
		JUNOS Services Jflow Container package [19.4R3.11]
		JUNOS Services Stateful Firewall [19.4R3.11]
		JUNOS Services NAT [19.4R3.11]
		JUNOS Services RPM [19.4R3.11]
		JUNOS Services SOFTWIRE [19.4R3.11]
		JUNOS Services Captive Portal and Content Delivery Container package [19.4R3.11]
		JUNOS Macsec Software Suite [19.4R3.11]
		JUNOS Services Crypto [19.4R3.11]
		JUNOS Services IPSec [19.4R3.11]
		JUNOS Services RTCOM [19.4R3.11]
		JUNOS Services SSL [19.4R3.11]
		JUNOS Services TCP-LOG [19.4R3.11]
		JUNOS DP Crypto Software Software Suite [19.4R3.11]
		JUNOS py-base-powerpc [19.4R3.11]
		JUNOS py-base2-powerpc [19.4R3.11]
		JUNOS py-extensions-powerpc [19.4R3.11]
		JUNOS py-extensions2-powerpc [19.4R3.11]
		JUNOS jsd [powerpc-19.4R3.11-jet-1]
		JUNOS Kernel Software Suite [19.4R3.11]
		JUNOS Routing Software Suite [19.4R3.11]
```

</p>
</details>
