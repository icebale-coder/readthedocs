title: DNS

# Протокол DNS

Disclaimer:
```bash
Моя заметка не претендую ни в коем случае на авторство или первоисточник...
Просто когда я начинал ее писать, то не думал, 
что она получится такой объемной и "развесистой"
```

## Предназначение
DNS (Domain Name System) - протокол предназначен для переобразования символического адреса в цифровой ip адрес (forward) 
```bash
например преобразования имени сайта в ip адрес для отправки на него запроса.
```

и наоборот - из цифрового адреса в символический (reverse)
```bash
например для защиты от спама, когда проверяется соответствие 
валидности PTR-записи почтового домена в соответствии с доменным именем 
почтового сервера при работе протокола SMTP.
```

DNS сервера имеют иерархичеческию систему.
Поиск доменного имени осуществляется рекурсивно.

## Алгоритм поиска

По сути клиент посылает на свой непосредстенный DNS запрос по резолву адреса и получает от своего сервера ответ
по интереующему его запросу.
*Свой сервер - это скорее всего будет сервер ISP или сервер, который настроен на маршрутизаторе клиента*


### Типы запросов
 - Прямые (forward)  
 - Обратные (reverse)

Алгоритм прямого и обратного поиска принципиально отличаются друг от друга

#### Пример прямого поиска (forward lookup)

Приведу процедуру поиска ip адреса для доменного имени mail.ru:
Если имя которое пытается разрешиться является доменным именем, 
которое принадлежит самому DNS серверу, то DNS сам его резолвит и отвечает в ответ.
Если же имя которое пытается разрешиться принадлежит другому домену, то DNS сервер выполняет рекурсивный запрос

Изначально DNS сервер знает корневые DNS сервера - это letter.root-servers.net, где "letter" - это буквы от "a" до "m"
Корневые сервера знают имена DNS серверов доменов первого уровня.

Прямой рекурсивный запрос выполняется следубщим образом:

 - Сначала клиент отправляет запрос своему непосредственному серверу DNS
 - Если доменное имя не принадлежит непосредственно текущему серверу, то 
   сервер отправляет запрос к корневому серверу DNS, который определяет DNS сервер 
   домена первого уровня, в нашем случае это "ru".
 - Получив ip адрес домена первого уровня текущий сервер обращается уже к нему, чтобы определить DNS второго уровня 
 - Получив ip адрес домена второго уровня текущий сервер обращается уже к нему, чтобы определить DNS третьего уровня,
 - ну и так далее... 
 - в конечном счете таким образом добираемся до DNS сервера который непосредственно отвечает за искомый домен
   и в конечном счете получаем ip адрес искомого нами адреса.


```bash
ns# dig +trace www.mail.ru

; <<>> DiG 9.11.5-P4-5.1+deb10u3-Debian <<>> +trace www.mail.ru
;; global options: +cmd
.                       490131  IN      NS      f.root-servers.net.
.                       490131  IN      NS      h.root-servers.net.
.                       490131  IN      NS      d.root-servers.net.
.                       490131  IN      NS      e.root-servers.net.
.                       490131  IN      NS      j.root-servers.net.
.                       490131  IN      NS      k.root-servers.net.
.                       490131  IN      NS      a.root-servers.net.
.                       490131  IN      NS      g.root-servers.net.
.                       490131  IN      NS      m.root-servers.net.
.                       490131  IN      NS      b.root-servers.net.
.                       490131  IN      NS      l.root-servers.net.
.                       490131  IN      NS      c.root-servers.net.
.                       490131  IN      NS      i.root-servers.net.
.                       490131  IN      RRSIG   NS 8 0 518400 20210514170000 20210501160000 14631 .                                                                                                                                           qYswRpuy2hymaLG0vlOLY08L6knybishnjH96oyB+tHLqF0pLttHTWD8 rkscPqVma1bz3ganjPVW3WPZ/6M2XJjwBUh+K8fVN                                                                                                                                          h0I/GeZRsc8vzVS I/Ecaw18ljQuFKXotWFD0tNxYbAy+WHGWA/7DvsG3qgyElIKFqej3vpJ FkM71KPa4ntw725gvQyKrKr+Jb                                                                                                                                          O84NkLynu+gsF67Zajzn05V/0+07cC klTceozWYDmOk3cIWL+6FM2bMQvqnT4d4O5An1zDoIygjTjiACJgg0hn 0MFwWEJQCUR                                                                                                                                          +qvbJIlzxGMV3AMMx0g5YEmmmzSzE2wNM6EJUhYNm9YK1 oMsCGQ==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

ru.                     172800  IN      NS      a.dns.ripn.net.
ru.                     172800  IN      NS      b.dns.ripn.net.
ru.                     172800  IN      NS      d.dns.ripn.net.
ru.                     172800  IN      NS      e.dns.ripn.net.
ru.                     172800  IN      NS      f.dns.ripn.net.
ru.                     86400   IN      DS      32215 8 2 803E2ADED022F18F59CBD68A39812BC2D224D2A68                                                                                                                                          E6D701BEDF62B7E 4CD92233
ru.                     86400   IN      RRSIG   DS 8 1 86400 20210515050000 20210502040000 14631 .                                                                                                                                           TAj6EeshP/JifZWNXklUO4a8/krGWfohh20Z1yie4i+3J+5R6AVKtT2t BqBZQX7vTLAAQwyP44ECwc+8OlrP8UCiQ2EcDstvgA                                                                                                                                          cwK5qPy0FXvUbt Dg/X2L5/hsWCDGm0FleVmeFyWy5sdxUkXRHC4GYeLGqVmRN3rqaqOno0 QN1OlCbraRlGaJ27cSSXk/UG5tf                                                                                                                                          hllVousLlfqMzbzNET1G9hrTWmwdr BLkaqDbO74AbRX/5XQieA7SZAsfZb2o6FgeB48GyfAv8IPU0sLv7521g 8zPbm+K1vZPT                                                                                                                                          oume2n4pIsRp73NizEtEUFrgWrW0t2Nt6R2o1Vnw4Ox0 qGGe4g==
;; Received 687 bytes from 199.9.14.201#53(b.root-servers.net) in 94 ms

MAIL.RU.                345600  IN      NS      ns1.mail.RU.
MAIL.RU.                345600  IN      NS      ns2.mail.RU.
MAIL.RU.                345600  IN      NS      ns3.mail.RU.
TDUI9D4JKUDS8B9T86GJ39PGFLCNLGM5.ru. 3600 IN NSEC3 1 1 3 00FF TE33KGTAQU9M5CRQ3IBSILQU11GFGTTT NS SOA RRSIG DNSKEY NSEC3PARAM
TDUI9D4JKUDS8B9T86GJ39PGFLCNLGM5.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210604130244 20210429102016 23976 ru. HDDqXzvkGf8i9quTNzfy13ybuGGSHdjx1n2uNVVTjSvt0U8SVoKa8Or7 HwdPnBBmanU+TIAkm+BKnFg2TpJONxO2z2/09GKboI0wnZoHz9k+PFVN YxYTT0/btYtgJmLNaW4Bp17LXNq65jtEJ5JNzz/H8a9kmvE2CJ7rKcAi rAg=
TRV524QID5HK416IEQOS38CEFO47L82D.ru. 3600 IN NSEC3 1 1 3 00FF TSBNSLGA2IQO725J1F7FBSFKSUK0FC11 NS DS RRSIG
TRV524QID5HK416IEQOS38CEFO47L82D.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210610015515 20210426222044 23976 ru. midt9N2zfR6ACrHsfW7WZnpSj8AsdJuOUuPncmC3R5cAwgl/Ajmhc5tJ 4djBxW+XfXy86WAkxQQDvSBToU3+E4QT2FX32GrLHMfycZynmVfDN/dp NVtsCW1PwHgtxGi5olI2+xm+bK4hqpqpar1vr04QQ06vS3UbpHeVV62N AzE=
;; Received 641 bytes from 193.232.156.17#53(f.dns.ripn.net) in 81 ms

www.mail.ru.            60      IN      A       217.69.139.70
www.mail.ru.            60      IN      A       94.100.180.70
mail.ru.                600     IN      NS      ns2.mail.ru.
mail.ru.                600     IN      NS      ns1.mail.ru.
mail.ru.                600     IN      NS      ns3.mail.ru.
;; Received 258 bytes from 217.69.139.112#53(ns1.mail.RU) in 58 ms
```


#### Пример обратного поиска (reverse lookup)

Обратный поиск строится на принципе, что для всех обратных зон 
в RIR (Regional Internet Registry) региональных интернет регистраторах.
Подробнее про RIR-ов и с чем их "едят" можно почитать напимер в [википедии](https://ru.wikipedia.org/wiki/%D0%A0%D0%B5%D0%B3%D0%B8%D0%BE%D0%BD%D0%B0%D0%BB%D1%8C%D0%BD%D1%8B%D0%B9_%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82-%D1%80%D0%B5%D0%B3%D0%B8%D1%81%D1%82%D1%80%D0%B0%D1%82%D0%BE%D1%80)


RIR-ов сейчас пять:

- ARIN — Северная Америка
- RIPE NCC — Европа, Ближний Восток и Центральная Азия
- APNIC — Азия и Тихоокеанский регион
- LACNIC — Латинская Америка и Карибский регион
- AfriNIC — Африки и регион Индийского океана

[Хорошая обзорная статья по работе с БД RIPE](https://3-info.ru/post/22205)

В их БД регистрируются и учитываются все [LIRы](https://ru.wikipedia.org/wiki/%D0%9B%D0%BE%D0%BA%D0%B0%D0%BB%D1%8C%D0%BD%D1%8B%D0%B9_%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82-%D1%80%D0%B5%D0%B3%D0%B8%D1%81%D1%82%D1%80%D0%B0%D1%82%D0%BE%D1%80) и их ресурсы, в том числе и объекты типа: "domain"

Объект типа "domain" предназначен как раз для учета реверсных(обратных) зон, в котором говорится, какой DNS сервер обслуживает данный диапазон адресов для реверсной зоны 

Объект "domain" имеет следующий вид:

```bash
ns# whois -r --sources RIPE 128.69.217.in-addr.arpa

% This is the RIPE Database query service.
% The objects are in RPSL format.
%
% The RIPE Database is subject to Terms and Conditions.
% See http://www.ripe.net/db/support/db-terms-conditions.pdf

% Note: this output has been filtered.
%       To receive output for a database update, use the "-B" flag.

% Information related to '128.69.217.in-addr.arpa'

domain:         128.69.217.in-addr.arpa
descr:          reverse delegation
admin-c:        VG659-RIPE
tech-c:         VG659-RIPE
zone-c:         VG659-RIPE
nserver:        ns1.mail.ru
nserver:        ns2.mail.ru
created:        2009-06-17T11:13:23Z
last-modified:  2014-04-04T11:52:07Z
source:         RIPE
mnt-by:         MNT-NETBRIDGE

% This query was served by the RIPE Database Query Service version 1.100 (ANGUS)
```

Реверсные зоны записываются наоборот, начиная с третьего октета и имеют следующий вид, например

"217.69.139.0/24 -> 128.69.217.in-addr.arpa "

При реверсном поиске также происходит рекурсия:

 - Сначала клиент отправляет запрос своему непосредственному серверу DNS
 - Если доменное обратная зона не принадлежит непосредственно текущему серверу, то 
   сервер отправляет запрос к корневому серверу DNS, который определяет DNS сервера 
   для реверсных доменных зон - это адреса DNS RIR серверов
 - Получив ip адрес DNS для реверсных доменных зон текущий сервер 
   обращается уже к нему и получает значение объекта "nserver"  из объекта "domain"
 - Далее получаем ip адрес для данного NS сервера (уже прямым реурсивным запросом) 
 - Обращаемся непосредственно к ip адресу сервера, который содержит искомую обратную зону
 - И в конечном счете получаем символическое имя, которое соответствует первоначальному ip адресу


```bash
ns# dig +trace -x 217.69.139.70

; <<>> DiG 9.11.5-P4-5.1+deb10u3-Debian <<>> +trace -x 217.69.139.70
;; global options: +cmd
.                       488354  IN      NS      c.root-servers.net.
.                       488354  IN      NS      e.root-servers.net.
.                       488354  IN      NS      d.root-servers.net.
.                       488354  IN      NS      g.root-servers.net.
.                       488354  IN      NS      i.root-servers.net.
.                       488354  IN      NS      j.root-servers.net.
.                       488354  IN      NS      m.root-servers.net.
.                       488354  IN      NS      k.root-servers.net.
.                       488354  IN      NS      a.root-servers.net.
.                       488354  IN      NS      b.root-servers.net.
.                       488354  IN      NS      f.root-servers.net.
.                       488354  IN      NS      h.root-servers.net.
.                       488354  IN      NS      l.root-servers.net.
.                       488354  IN      RRSIG   NS 8 0 518400 20210514170000 20210501160000 14631 . qYswRpuy2hymaLG0vlOLY08L6knybishnjH96oyB+tHLqF0pLttHTWD8 rkscPqVma1bz3ganjPVW3WPZ/6M2XJjwBUh+K8fVNh0I/GeZRsc8vzVS I/Ecaw18ljQuFKXotWFD0tNxYbAy+WHGWA/7DvsG3qgyElIKFqej3vpJ FkM71KPa4ntw725gvQyKrKr+JbO84NkLynu+gsF67Zajzn05V/0+07cC klTceozWYDmOk3cIWL+6FM2bMQvqnT4d4O5An1zDoIygjTjiACJgg0hn 0MFwWEJQCUR+qvbJIlzxGMV3AMMx0g5YEmmmzSzE2wNM6EJUhYNm9YK1 oMsCGQ==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

in-addr.arpa.           172800  IN      NS      c.in-addr-servers.arpa.
in-addr.arpa.           172800  IN      NS      a.in-addr-servers.arpa.
in-addr.arpa.           172800  IN      NS      b.in-addr-servers.arpa.
in-addr.arpa.           172800  IN      NS      f.in-addr-servers.arpa.
in-addr.arpa.           172800  IN      NS      e.in-addr-servers.arpa.
in-addr.arpa.           172800  IN      NS      d.in-addr-servers.arpa.
in-addr.arpa.           86400   IN      DS      47054 8 2 5CAFCCEC201D1933B4C9F6A9C8F51E51F3B39979058AC21B8DF1B1F2 81CBC6F2
in-addr.arpa.           86400   IN      DS      53696 8 2 13E5501C56B20394DA921B51412D48B7089C5EB6957A7C58553C4D4D 424F04DF
in-addr.arpa.           86400   IN      DS      63982 8 2 AAF4FB5D213EF25AE44679032EBE3514C487D7ABD99D7F5FEC3383D0 30733C73
in-addr.arpa.           86400   IN      RRSIG   DS 8 2 86400 20210515000000 20210501230000 6795 arpa. GrIKoFDrJeSVOzTMNFjVeofRuJa5cNDRRWA4CUiyze6wr2NGtViKddmW dYWGFnscCNMKoR45lhM30xgucFX+Rhg+1KjkYPmt8GnTTigsjvsAoIvB S6NFt4i12GsMqHXqmOzvF2CW74rQmX2Eph4Ijuft7uVkMh2mYtDRQd3f 8Z4R02tVM/9xDqajUqcz5uRcBprKjaBkgkgytGmyjTnBBLhvyYhc/JNm 7wWU3LM/fsQbQKZMuKLLJZ0Obi7Ucmytjucz9X++NX98aqdiIX99xl65 ZjIVzi9tJvLXsJrvtNCAS10tbf/6nLAaq37ryNeftmofJ0I3SoIrVw0d Jd59gA==
;; Received 911 bytes from 192.36.148.17#53(i.root-servers.net) in 68 ms

217.in-addr.arpa.       86400   IN      NS      ns3.lacnic.net.
217.in-addr.arpa.       86400   IN      NS      ns3.afrinic.net.
217.in-addr.arpa.       86400   IN      NS      ns4.apnic.net.
217.in-addr.arpa.       86400   IN      NS      pri.authdns.ripe.net.
217.in-addr.arpa.       86400   IN      NS      rirns.arin.net.
217.in-addr.arpa.       86400   IN      DS      35000 8 2 4FA31022B9C4BB89DA583B336F364E364CCFE05AC4A9D89E104F7B6F 2DDC23AF
217.in-addr.arpa.       86400   IN      RRSIG   DS 8 3 86400 20210519233647 20210429000003 41136 in-addr.arpa. h7yjtxDZ6e9EhbHX90xtZ/zl0Kh3QC3oWClXUxVuJm1TeGQ3LBgUHQLQ zVnuGrQu0Fuai4SJdJhAY7oyNoamlthe4LetPd8KeKdGjAqTWqBPHRJ+ ZqM7vieK4U5zoT43drYlJDfZkVk+1B5LasRQPLqCFiTZ2xIp+59LEVQz sUs=
;; Received 409 bytes from 196.216.169.10#53(c.in-addr-servers.arpa) in 269 ms

139.69.217.in-addr.arpa. 172800 IN      NS      ns1.mail.ru.
139.69.217.in-addr.arpa. 172800 IN      NS      ns2.mail.ru.
139.69.217.in-addr.arpa. 3600   IN      NSEC    14.69.217.in-addr.arpa. NS RRSIG NSEC
139.69.217.in-addr.arpa. 3600   IN      RRSIG   NSEC 8 5 3600 20210512031608 20210428014608 45606 217.in-addr.arpa. U87Y5uJOsnfgh/AL+7iRYp9mAMG4SF4eVUGWPnwjapl4+tRUdcx7hM0N 6Uw4Lcpw4WDK+zcWy+6CI/hnBHuqKJ9FoqaXhzNqEiewPhdzpPaSN3HS ezLylyTcE9LKqYw2SiojkVF0VP1D29juqVZNG1eu7+pjg0XlBBrJMy4i 0WI=
;; Received 318 bytes from 193.0.9.5#53(pri.authdns.ripe.net) in 72 ms

70.139.69.217.in-addr.arpa. 3600 IN     PTR     www.mail.ru.
139.69.217.in-addr.arpa. 3600   IN      NS      ns2.mail.ru.
139.69.217.in-addr.arpa. 3600   IN      NS      ns1.mail.ru.
;; Received 204 bytes from 217.69.139.112#53(ns1.mail.ru) in 57 ms
```

### Виды запросов
 Видов запорсов достаточно много приведу наиболее часто используемые:

 - A-запись - доменное имя, которому соответствует ipv4 адрес (forward resolve)
 - AAAA-запись - доменное имя, которому соответствует ipv6 адрес (forward resolve)
 - CNAME-запись - запись ссылка на другое доменное имя
 - NS-запись - доменное имя DNS сервера, которому соответствует ip адрес (forward resolve)
 - MX-запись - доменное имя почтового сервера, которому соответствует ip адрес (forward resolve)
 - PTR-запись - ip адрес, которому соответствует доменное имя (reverse resolve)
 - TXT-запись - "хитрая" запись для проверки валидности например ssl-сертификата 
                или еще какой либо "валидности", которую знает валидирующий сервис,
                например защите от спама для почтовых серверов...

## Формат протокола DNS

Сама работа DNS протокола подробно изложена в [RFC1035](https://www.ietf.org/rfc/rfc1035.txt)

[Вот](https://habr.com/ru/post/478652/), вольный его перевод с habr-а

Вкраце изложу формат самого протокола, не буду "изобретать велосипед", просто закопипастить структуру протокола [отсюда](https://habr.com/ru/post/478652/)

### Структура пакета DNS
```bash
    +---------------------+
    |        Header       | Заголовок
    +---------------------+
    |       Question      | Секция запросов
    +---------------------+
    |        Answer       | Секция ответа
    +---------------------+
    |      Authority      | Секция ответа об уполномоченных серверах
    +---------------------+
    |      Additional     | Секция ответа дополнительных записей
    +---------------------+

- Header — Заголовок DNS пакета, состоящий из 12 октет.

- Question section — в этой секции DNS-клиент передает 
  запросы DNS-серверу сообщая о том, для какого имени необходимо разрешить
  (зарезолвить) запись DNS, а также какого типа (NS, A, TXT и т.д.). 
  Сервер при ответе, копирует эту информацию и отдает клиенту обратно 
  в этой же секции.

- Answer section — сервер сообщает клиенту ответ или несколько ответов
  на запрос, в котором сообщает вышеуказанные данные.

- Authoritative Section — содержит сведения о том, с помощью каких 
  авторитетных серверов было получена информация включенная 
  в секцию DNS-ответа.

- Additional Record Section — дополнительные записи, 
  которые относятся к запросу, но не являются строго ответами на вопрос.    
```

### Структура заголовка DNS

```bash
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      ID                       |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |QR|   Opcode  |AA|TC|RD|RA|   Z    |   RCODE   |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    QDCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    ANCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    NSCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                    ARCOUNT                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+

- ID (16 бит) — данное поле используется как уникальный идентификатор транзакции. 
  Указывает на то, что пакет принадлежит одной и той же сессии “запросов-ответов” 
  и занимает 16 бит.

- QR (1 бит) — данный бит служит для индентификации того, является ли пакет 
  запросом (QR = 0) или ответом (QR = 1).

- Opcode (4 бита) — с помощью данного кода клиент может указать тип запроса, 
  где обычное значение:
	-0 — стандартный запрос,
	-1 — инверсный запрос,
	-2 — запрос статуса сервера.
	-3-15 – зарезервированы на будущее.

- AA (1 бит) — данное поле имеет смысл только в DNS-ответах от сервера и сообщает 
  о том, является ли ответ авторитетным либо нет.

- TC (1 бит) — данный флаг устанавливается в пакете ответе в том случае,
  если сервер не смог поместить всю необходимую информацию в пакет из-за 
  существующих ограничений. TC флаг говорит о том, что ответ сервера 
  превышает 512 bytes и как раз и является сигналом, для клиента 
  "переинициировать" соединение из udp в tcp.


- RD (1 бит) — этот однобитовый флаг устанавливается в запросе 
  и копируется в ответ. Если он флаг устанавливается в запросе — это значит, 
  что клиент просит сервер не сообщать ему промежуточных ответов, 
  а вернуть только IP-адрес.

- RA (1 бит) — отправляется только в ответах, и сообщает о том, 
  что сервер поддерживает рекурсию

- Z (3 бита) — являются зарезервированными и всегда равны нулю.

- RCODE (4 бита) — это поле служит для уведомления клиентов о том, 
  успешно ли выполнен запрос или с ошибкой.
	0 — значит запрос прошел без ошибок;
	1 — ошибка связана с тем, что сервер не смог понять форму запроса;
	2 — эта ошибка с некорректной работой сервера имен;
	3 — имя, которое разрешает клиент не существует в данном домене;
	4 — сервер не может выполнить запрос данного типа;
	5 — этот код означает, что сервер не может удовлетворить запроса клиента
	    в силу административных ограничений безопасности.

- QDCOUNT(16 бит) – количество записей в секции запросов
- ANCOUNT(16 бит) – количество записей в секции ответы
- NSCOUNT(16 бит) – количество записей в Authority Section
- ARCOUNT(16 бит) – количество записей в Additional Record Section
```

### Структура секции запроса
```bash
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                                               |
    /                     QNAME                     /
    /                                               /
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     QTYPE                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     QCLASS                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+

- QNAME — Каждая запись запроса и ответа начинается с NAME.
  Это доменное имя, к которому привязана или которому “принадлежит” данная запись.
  Она закодирована как серия меток. На этом моменте следует остановиться 
  несколько поподробнее.

Исходный протокол DNS предусматривает два типа меток, 
которые определяются первыми двумя битами:

00 (стандартная метка) – значит, остальные 6 бит определяют длину метки, 
   за которым следует данное количество октетов. Соответственно, длина метки 
   не может быть более 63 байта (Например, nslookup выдаст сообщение 
   “is not a legal name (label too long)” 
   при попытке отрезолвить хост с длинной меткой). Заканчивается запись кодом 0x00.

11 (сжатая метка) – тогда последующие 14 бит определяют ссылку 
   на начальный адрес серии меток. 

Так же метка может содержать значение 0x00 (нулевая длина), 
означает что это корневое доменное имя (root).

Максимальная длина NAME <= 255. Это создано ради простоты реализации.

QTYPE — Тип записи DNS, которую мы ищем (NS, A, TXT и т.д.).
QCLASS — Определяющий класс запроса (IN для Internet).
```

### Структура секции ответов

```bash
                                    1  1  1  1  1  1
      0  1  2  3  4  5  6  7  8  9  0  1  2  3  4  5
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                                               |
    /                                               /
    /                      NAME                     /
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TYPE                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                     CLASS                     |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                      TTL                      |
    |                                               |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+
    |                   RDLENGTH                    |
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--|
    /                     RDATA                     /
    /                                               /
    +--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+--+

- NAME — Такой же формат, что и QNAME в секции запроса.
- TYPE — тип ресурсной записи. Определяет формат и назначение данной 
  ресурсной записи.
- CLASS — класс ресурсной записи; теоретически считается, что DNS может 
  использоваться не только с TCP/IP, но и с другими типами сетей, 
  код в поле класс определяет тип сети. В основном IN для Internet (Код 0x0001)
- TTL — (Time To Live) — допустимое время хранения данной ресурсной записи 
  в кэше неответственного DNS-сервера.
- RDLENGTH — длина поля данных (RDATA).
- RDATA — поле данных, формат и содержание которого зависит от типа записи.
```
протокол DNS использует порт 53: udp/53 или tcp/53

!!!note "Важный факт" 
		По [RFC-7766](https://tools.ietf.org/html/rfc7766#ref-RRL1) - Размер UDP датаграммы не должен превышать 512 bytes. 
		Если размер данных в протоколе DNS превышает 512 byte, то эти данные передаются уже в tcp сегменте. 
		За это отвечает TC флаг в заголовке DNS при ответе, если при ответе "TC=1", то DNS запрос из udp "переинициируется"
		уже в tcp соединение.

[In the absence of EDNS0 (Extension Mechanisms for DNS 0 [RFC6891];
   see below), the normal behaviour of any DNS server that needs to send
   a UDP response that would exceed the 512-byte limit is for the server
   to truncate the response so that it fits within that limit and then
   set the TC flag in the response header.  When the client receives
   such a response, it takes the TC flag as an indication that it should
   retry over TCP instead.](https://tools.ietf.org/html/rfc7766#ref-RRL1)

Пример такого запроса DNS в wireshark, где при первоначальном запросе UDP сервер "просит" сделать перезапрос в TCP приведен [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/dns-udp-to-tcp-telegram-for-txt-record.pcapng)

Пример стандартного запроса DNS в wireshark, приведен [здесь](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/dns-A-rec-mail.ru-udp.pcapng)



