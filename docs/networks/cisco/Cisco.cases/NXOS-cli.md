title: NX-OS cli

# NX-OS cli

Disclaimer:

```bash
еже еси на vpc... (c) Ваня Т.
```
NX-OS - это вообще весьма большая тема для разговора...
Здесь я постараюсь описать свои рабочии моменты при работе с данной ОС,
в частности "vpc", "conf sync"

## vpc

Настройка vpc - базовый момент при парной работе Nexus.
Я отдельно освещу данную тему... когда найдется время...
Disign: Peer-Link, Keep-Alive link, Single-Side, Double-Side, Back-to-Back.

Вот дельная статья по дизайну и принципу работы vpc: [Nexus vpc guide](https://www.firewall.cx/cisco-technical-knowledgebase/cisco-data-center/1208-nexus-vpc-configuration-design-operation-troubleshooting.html)


###  sh vpc consistency-parameters global

``` bash
"sh vpc consistency-parameters global" - Посмотреть состояние общее по vpc
```


### sh vpc consistency-parameters vpc 50

``` bash
"sh vpc consistency-parameters vpc 50" - Посмотреть состояние определенного vpc 
   ! в данном примере влан 906 засеспенджен, потому что ее нет еще в vpc-peer-link

#sh vpc consistency-parameters vpc 50
   Legend:
        Type 1 : vPC will be suspended in case of mismatch

Name                        Type  Local Value            Peer Value             
-------------               ----  ---------------------- -----------------------
STP Port Type               1     Edge Port              Edge Port             
STP Port Guard              1     Default                Default               
STP MST Simulate PVST       1     Default                Default               
mode                        1     on                     on                    
delayed-lacp                1     disabled               disabled              
Speed                       1     40 Gb/s                40 Gb/s               
Duplex                      1     full                   full                  
Port Mode                   1     trunk                  trunk                 
Native Vlan                 1     1                      1                     
MTU                         1     1500                   1500                  
Dot1q Tunnel                1     no                     no                    
Switchport Isolated         1     0                      0                     
lag-id                      1                                                  
vPC card type               1     N3K                    N3K                   
Allowed VLANs               -     14,54-55,57,62,66-70,7 14,54-55,57,62,66-70,7
                                  5,77-79,81,83-84,87,89 5,77-79,81,83-84,87,89
                                  ,92,94,97-98,103,107-1 ,92,94,97-98,103,107-1
                                  08,139,343,402-403,903 08,139,343,402-403,903
                                  ,905-906,1053,1235-123 ,905-906,1053,1235-123
                                  7,3948,3954            7,3948,3954           
Local suspended VLANs       -     906   
```

## conf sync

Disclaimer:
```bash
"conf sync" в Nexus - это как занятие сексом:
вроде все это уже делал, знаешь, 
но по ходу действия приходится что-то вспоминать на ходу...
По этому собственно "записки на полях" - "как заниматься "сексом".
Вдруг кто еще забыл... )
```

Если в двух словах, "conf sync" -  это возможность автоматической синхронизации.
По сути есть "conf t" - в котором хранятся индивидуальные настройки коммутатора и 
есть кусочек конфигурации, который синкается и соответственно будет одинаковым.
Можно сказать, что "conf t" + "conf sync" составляют "global conf".

!!!warning "Важное замечание"
					  Нужно четко определиться с тем, что у вас будет храниться именно в "sync"
            конфигурации, иначе можно в какой то момент сделать себе очень "больно".

В моем случае мы храним в "sync" виртуальные сущности, такие как общие вланы, общие портчаны, например обязательно "peer-link"
но принципиально например не храним настройки интерфейсов.

!!!warning "Важно знать"
				    Для работы "conf sync" использует multicast. 
            По этому необходимо если у вас keep-alive между участниками vpc домена подключен не напрямую,
            а например через промежуточные коммутаторы, то на этих промежуточных коммутаторах должен быть разрешен
            multicast - знаю, наступал на эти "грабли"


Что касается документации, то ее много по данному поводу.
Вот доки, которыми пользовался я при работе с "conf sync".
Знаем, плавали...

- [настройка "conf sync"](https://nexp.com.ua/technologies/nx-os/vpc-domain-configuration-synchronization-guidelines/)

- [траблшутинг "conf sync"](https://nexp.com.ua/technologies/nx-os/troubleshooting-nx-os-config-sync/)

### Команды для работы с "conf sync":

#### sh run switch-profile

```bash
"sh run switch-profile" - показать содержимое конфигурации, находящейся в "conf sync"
```
#### conf sync

```bash
"conf sync" - перейти в режим конфигурации "conf sync"
```
#### switch-profile N0-N2

```bash
"switch-profile N0-N2" - перейти в режим  редактирования конфигурации, 
                          где N0-N2 - название профиля
```

#### show switch-profile status

```bash
"show switch-profile status" - можно посмотреть состояние switch-profile
                              всё ли с ним в порядке.
```

После этого можно вносить команды в конфигурацию как и в обыкновенном "conf t"
Например мы внесли новые вланы:
```bash
vlan 111
 name office
vlan 222
 name client
```

Данные команды перед их применением на конфигурации попадают в буфер, где хранятся  до их применения на оборудовании.
С этим буфером можно произвести определенные манипуляции:

#### show switch-profile buffer

```bash
"show switch-profile buffer" - просмотр буфера
1. vlan 111
 1.1. name office
2. vlan 222
 2.1. name client
```

Можно удалить либо весь буфер либо определенную команду из него.

#### buffer-delete

```bash
"buffer-delete" - удалить весь буфер.
```

#### commit

```bash
"commit" - записать(применить) содержимое буфера в конфигурацию "conf sync".
           При данном действии как раз и происходит синхронизация конфигурации со вторым пиром.
```