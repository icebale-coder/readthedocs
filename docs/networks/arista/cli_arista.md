title: Arista cli

!!!warning "Disclaimer"
          Перейдя работать в DС довелось наконец первый раз пощупать Arist-ы
          по сути "Arista Networks" сейчас - это основной поставщик оборудования для ЦОД-ов у "рука-лица"

# Arista cli:
```bash
Arista cli - явно выраженный "cisco-like" - и не мудрено (костяк основателей как раз выхдцы из cisco)
Рассмотрю cli на примере работы с Arista DCS-7020SR-24C2-R
```

## Работа в конфигурационном режиме:
```bash
  У Arist-ы есть много вариантов работы с конфигурацией
      Arista#configure ?
          checkpoint  Creating a new checkpoint
          convert     Perform configuration conversion
          lock        Acquire the configure lock
          network     Read Configuration from the Network
          replace     Replace configuration state
          session     Enter configuration session; commands applied only on commit
          terminal    Config mode
          unlock      Remove the configure lock
          <cr>        
```

Рассмотрим 2 основных режима работы с конфигурацией:
### 1. "configure terminal"
"configure terminal" - это аналог работы с cisco без commit-a
```bash
  Тут все что конфигурируется применяется сразу - полная аналогия с командами cisco:
      Arista#conf t
      Arista(config)#int e22                    
      Arista(config-if-Et22)#description test                    
      Arista(config)#do sh run int e22
          interface Ethernet22
              description test
              mtu 9214
              no switchport                    
```
### 2. "configure session"
"configure session"  - это работа через систему commit-ов (аналог работы c juniper "configure private"

```bash
"Вход и редактирование конфига в режиме конфигурации сессии"
Arista#conf session
Arista(config-s-sess-2)#int e22
Arista(config-s-sess-2-if-Et22)#desc test-new 

"Просмотр, что будет изменено в конфигурации после коммита"
Arista(config-s-sess-2-if-Et22)#sh session-config diffs 
    --- system:/running-config
    +++ session:/sess-2174--797967552-1-session-config
    @@ -130,7 +130,7 @@
        no switchport
    !
    interface Ethernet22
    -   description test
    +   description test-new
        mtu 9214
        no switchport
!Arista(config-s-sess-2-if-Et22)#commit 

после коммита сразу выходит из режима "configure-session"
```

### Работа с rollback/commit

#### Просмотр деталей текущих сессий
<details><summary>sh configuration sessions detail</summary>
<p>

```bash
"Arista#sh configuration sessions detail"
    Maximum number of completed sessions: 1
    Maximum number of pending sessions: 5

    Name                      State           User       Terminal       Commit Time Left            Completed Time    Committed By 
    ---------------------- --------------- ---------- -------------- ---------------------- ------------------------- ------------ 
    sess-2174--797967552-1    completed                                                        2021-08-08 13:50:00       user4 
    sess-2174--797967552-2    pending                                                                                              
```
</p>
</details>

#### Просмотр сохрненных конфигураций
<details><summary>Просмотр сохрненных конфигураций</summary>
<p>

```bash
"Arista#sh configuration checkpoints"
    Maximum number of checkpoints: 20
    Filename              Date                   User 
    -------------- ------------------------- ----------- 
    ckp-20220805-3    2021-01-01 15:21:29      user1 
    ckp-20220805-4    2021-01-01 15:42:52      user1 
    ckp-20220805-5    2021-01-01 15:44:58      user1 
    ckp-20220805-6    2021-01-01 15:47:50      user1 
    ckp-20220805-7    2021-01-01 15:54:19      user1 
    ckp-20220805-8    2021-01-01 16:09:23      user1 
    ckp-20220808-0    2021-01-01 15:08:51      user1 
    ckp-20220808-1    2021-01-01 15:10:00      user1 
    ckp-20220905-0    2021-02-02 16:22:36      user1 
    ckp-20220905-1    2021-02-02 17:11:19      user2 
    ckp-20220905-2    2021-02-02 17:11:41      user2 
    ckp-20220928-0    2021-03-03 12:37:01      user1 
    ckp-20221028-0    2021-04-04 17:50:29      user3 
    ckp-20221028-1    2021-04-04 18:03:11      user3 
    ckp-20221028-2    2021-04-04 18:03:25      user3 
    ckp-20221031-0    2021-05-05 10:00:26      user3 
    ckp-20221031-1    2021-05-05 10:11:10      user3 
    ckp-20221103-0    2021-06-06 18:09:10      user3 
    ckp-20221111-0    2021-07-07 15:26:48      user3 
    ckp-20221125-0    2021-08-08 13:50:00      user4 


"Текущая конфигурация"               
"Arista#sh run int e22"
    interface Ethernet22
    description test-new
    mtu 9214
    no switchport

"Восстановление до определенного checkpointa"
!Arista#configure checkpoint restore ?
    WORD  Restore checkpoint name

"!!!Важно - откат конфигурации происходит сразу (без чеков и коммитов) -  в отличии от Juniper-а"

"Arista#conf checkpoint restore ckp-20221125-0"

"Проверяем что изменилось"
Arista#sh run int e22
    interface Ethernet22
    description test
    mtu 9214
    no switchport

                   
"diff между изменениями  в конфигурации"
"Разница между ckp-20221125-1 и ckp-20221125-0"
    Arista#diff checkpoint:ckp-20221125-1 checkpoint:ckp-20221125-0
      --- checkpoint:ckp-20221125-1
      +++ checkpoint:ckp-20221125-0
      @@ -194,8 +194,6 @@
      interface Ethernet1
          description RouterA
          no switchport
      +   ip address 1.1.1.1/31
      !
      interface Ethernet2
          description RouterB

"Разница между текущей конфигурацией и ckp-20230412-0"
    Arista#diff checkpoint:ckp-20230412-0 running-config 
      --- checkpoint:ckp-20230412-0
      +++ system:/running-config
      @@ -247,6 +247,7 @@
          spanning-tree bpduguard enable
      !
      interface Ethernet21
      +   shutdown
          switchport trunk native или vlan 1000
          switchport mode trunk
          sflow enable

"Защита от дурака"
"Можно задать таймер применения конфига - если не закомиттить в течении данного времени, то конфиг откатится назад"
  Arista(config-s-sess-2-if-Et22)#commit ?
      timer  commit session with a timeout. If not committed within this time, config will be reverted.
```
</p>
</details>


## Arista - что "под капотом"
Как и в Джуне на ARIST-e можно провалиться в шелл:

### Провалиться в Linux

<details><summary>Arista#bash</summary>
<p>

```bash
"Arista#bash"
    Arista Networks EOS shell

    Посмотреть версию ОС - и это мой любимый CentOS!!! 
    Жаль конечно, что RedHat-овцы сначала купили его, а потом спустили в "сотртир"... 
    И по сути сейчас основной опенсорс мейнстрим это Debian.
    Но это так лирика - шаг в сторону...
    Продолжим...
    ![user@Arista ~]$ cat /etc/*release*
        Arista Networks EOS 4.25.4M
        Arista Networks EOS 4.25.4M
        # os-release generated by Eos-release-4.25.4 for platform i386_el7

        NAME="CentOS Linux"
        VERSION="7 (AltArch)"
        ID="centos"
        ID_LIKE="rhel fedora"
        VERSION_ID="7"
        PRETTY_NAME="CentOS Linux 7 (AltArch)"
        ANSI_COLOR="0;31"
        CPE_NAME="cpe:/o:centos:centos:7"
        HOME_URL="https://www.centos.org/"
        BUG_REPORT_URL="https://bugs.centos.org/"

        CENTOS_MANTISBT_PROJECT="CentOS-7"
        CENTOS_MANTISBT_PROJECT_VERSION="7"
        REDHAT_SUPPORT_PRODUCT="centos"
        REDHAT_SUPPORT_PRODUCT_VERSION="7"

        CentOS Linux release 7.8.2003 (AltArch)
        
        Arista Networks EOS 4.25.4M
        Arista Networks EOS shell

"Посмотреть размер памяти"
    [user@Arista ~]$ cat /proc/meminfo
        MemTotal:       32879560 kB
        MemFree:        27733380 kB
        MemAvailable:   30153416 kB
        Buffers:          246528 kB
        Cached:          3214368 kB
        SwapCached:            0 kB
        Active:          1913672 kB
        Inactive:        2505804 kB
        Active(anon):    1144896 kB
        Inactive(anon):    69836 kB
        Active(file):     768776 kB
        Inactive(file):  2435968 kB
        Unevictable:           0 kB
        Mlocked:               0 kB
        SwapTotal:             0 kB
        SwapFree:              0 kB
        Dirty:                16 kB
        Writeback:             0 kB
        AnonPages:        958576 kB
        Mapped:           755644 kB
        Shmem:            256156 kB
        Slab:             450696 kB
        SReclaimable:     143756 kB
        SUnreclaim:       306940 kB
        KernelStack:        7536 kB
        PageTables:        96752 kB
        NFS_Unstable:          0 kB
        Bounce:                0 kB
        WritebackTmp:          0 kB
        CommitLimit:    16439780 kB
        Committed_AS:    3693512 kB
        VmallocTotal:   34359738367 kB
        VmallocUsed:           0 kB
        VmallocChunk:          0 kB
        Percpu:             4512 kB
        HardwareCorrupted:     0 kB
        CmaTotal:              0 kB
        CmaFree:               0 kB
        HugePages_Total:       0
        HugePages_Free:        0
        HugePages_Rsvd:        0
        HugePages_Surp:        0
        Hugepagesize:       2048 kB
        Hugetlb:               0 kB
        
        DirectMap4k:      186172 kB
        DirectMap2M:     8185856 kB
        
            Посмотреть версию ОС
        irectMap1G:    27262976 kB
        [user@Arista ~]$ cat /proc/meminfo
        MemTotal:       32879560 kB
        MemFree:        27733880 kB
        MemAvailable:   30154144 kB
        Buffers:          246728 kB
        Cached:          3214380 kB
        SwapCached:            0 kB
        Active:          1913720 kB
        Inactive:        2505808 kB
        Active(anon):    1144740 kB
        Inactive(anon):    69840 kB
        Active(file):     768980 kB
        Inactive(file):  2435968 kB
        Unevictable:           0 kB
        Mlocked:               0 kB
        SwapTotal:             0 kB
        SwapFree:              0 kB
        Dirty:                16 kB
        Writeback:             0 kB
        AnonPages:        958420 kB
        Mapped:           755612 kB
        Shmem:            256164 kB
        Slab:             450620 kB
        SReclaimable:     143808 kB
        SUnreclaim:       306812 kB
        KernelStack:        7488 kB
        PageTables:        96292 kB
        NFS_Unstable:          0 kB
        Bounce:                0 kB
        WritebackTmp:          0 kB
        CommitLimit:    16439780 kB
        Committed_AS:    3682148 kB
        VmallocTotal:   34359738367 kB
        VmallocUsed:           0 kB
        VmallocChunk:          0 kB
        Percpu:             4512 kB
        HardwareCorrupted:     0 kB
        CmaTotal:              0 kB
        CmaFree:               0 kB
        HugePages_Total:       0
        HugePages_Free:        0
        HugePages_Rsvd:        0
        HugePages_Surp:        0
        Hugepagesize:       2048 kB
        Hugetlb:               0 kB
        
        DirectMap4k:      186172 kB
        DirectMap2M:     8185856 kB
        
            Посмотреть версию ОС
        
        irectMap1G:    27262976 kB

"Посмотреть версию ОС"
  user@Arista ~]$ uname -a
      Linux Arista.pod1.z502 4.19.142.Ar-22394741.4254M #1 SMP PREEMPT Tue May 25 11:41:11 PDT 2021 x86_64 x86_64 x86_64 GNU/Linux
```
</p>
</details>


### Примеры других настроек:
#### Ручное задание на интерфейсе типа/скорости SFP+ модуля

```bash
  !При такой настройке задается тип и скорость работы модуля
      interface Ethernet25/1
          ...
          transceiver media override 100gbase-cwdm4                
```

#### Просмотр состояние питания

```bash
"sh environment power"    
Arista# sh environment power 
    Power                            Input  Output  Output
    Supply Model           Capacity Current Current  Power Status                Uptime
    ------ --------------- -------- ------- ------- ------ ---------- -----------------
    1      PWR-511-AC-BLUE     500W   0.47A   7.38A  88.2W Ok         317 days, 2:54:38
    2      PWR-511-AC-BLUE     500W   0.00A   0.00A   0.0W Power Loss           Offline
    Total  --                  500W      --      --  88.2W --                        --
```

#### Приколы с BGP shutdown

```bash
"Если имеется конструкция типа:"
  router bgp 1111       
      neighbor 1.1.1.1 shutdown
"то отменить ее не так и просто"
"при задании команды с "no" конструкция поведет себя не так как ожидалось бы"
  !Arista(config-s-sess-2-router-bgp)#no neighbor 1.1.1.1 shutdown
  !Arista(config-s-sess-2-router-bgp)#sh session-config diffs 
      --- system:/running-config
      +++ session:/sess-2095--770704576-1-session-config
      @@ -1227,7 +1227,7 @@
      -      neighbor 1.1.1.1 shutdown
      +      no neighbor 1.1.1.1 shutdown
"Для корректного восстановления сессии при shutdown-e нужно использовать конструкцию default, т.е."                            
Arista(config-s-sess-2-router-bgp)##default neighbor 1.1.1.1 shutdown             
"Тогда всё будет норм"
  !Arista(config-s-sess-2-router-bgp)#sh session-config diffs 
      --- system:/running-config
      +++ session:/sess-2095--770704576-1-session-config
      @@ -1227,7 +1227,7 @@
      -      neighbor 1.1.1.1 shutdown
```                  