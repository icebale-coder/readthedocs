title: Base commands

## Полезные команды/программы

```bash
# полезная программа для генерации префиксов которые есть в AS, согласно БД RIR
bgpq4 
```

Примеры использования - построения префиксов из БД RIPE Орион-Телекома AS31257

Cisco IOS/ IOS XE

<details><summary>bgpq4 -l PL-ORION AS31257</summary>
<p>

```bash
no ip prefix-list PL-ORION
ip prefix-list PL-ORION permit 80.65.16.0/20
ip prefix-list PL-ORION permit 80.65.16.0/21
ip prefix-list PL-ORION permit 80.65.16.0/24
ip prefix-list PL-ORION permit 80.65.20.0/24
ip prefix-list PL-ORION permit 80.65.24.0/21
ip prefix-list PL-ORION permit 81.25.76.0/22
ip prefix-list PL-ORION permit 81.25.76.0/23
ip prefix-list PL-ORION permit 81.25.76.0/24
ip prefix-list PL-ORION permit 81.25.77.0/24
ip prefix-list PL-ORION permit 81.25.78.0/23
ip prefix-list PL-ORION permit 81.25.78.0/24
ip prefix-list PL-ORION permit 81.25.79.0/24
ip prefix-list PL-ORION permit 86.62.6.0/24
ip prefix-list PL-ORION permit 94.73.192.0/18
ip prefix-list PL-ORION permit 94.73.192.0/19
ip prefix-list PL-ORION permit 94.73.202.0/24
ip prefix-list PL-ORION permit 94.73.224.0/19
ip prefix-list PL-ORION permit 94.73.240.0/20
ip prefix-list PL-ORION permit 94.73.240.0/24
ip prefix-list PL-ORION permit 94.73.241.0/24
ip prefix-list PL-ORION permit 94.73.242.0/24
ip prefix-list PL-ORION permit 94.73.243.0/24
ip prefix-list PL-ORION permit 94.73.244.0/24
ip prefix-list PL-ORION permit 94.73.245.0/24
ip prefix-list PL-ORION permit 94.73.246.0/24
ip prefix-list PL-ORION permit 94.73.247.0/24
ip prefix-list PL-ORION permit 94.73.248.0/24
ip prefix-list PL-ORION permit 94.73.249.0/24
ip prefix-list PL-ORION permit 94.73.250.0/24
ip prefix-list PL-ORION permit 94.73.251.0/24
ip prefix-list PL-ORION permit 94.73.252.0/24
ip prefix-list PL-ORION permit 109.226.192.0/18
ip prefix-list PL-ORION permit 109.226.192.0/19
ip prefix-list PL-ORION permit 109.226.224.0/19
ip prefix-list PL-ORION permit 134.19.128.0/21
ip prefix-list PL-ORION permit 171.33.248.0/21
ip prefix-list PL-ORION permit 171.33.250.0/23
ip prefix-list PL-ORION permit 171.33.252.0/23
ip prefix-list PL-ORION permit 176.116.160.0/20
ip prefix-list PL-ORION permit 176.116.160.0/21
ip prefix-list PL-ORION permit 176.116.168.0/21
ip prefix-list PL-ORION permit 185.84.172.0/23
ip prefix-list PL-ORION permit 185.84.172.0/24
ip prefix-list PL-ORION permit 185.84.173.0/24
```
</p>
</details>

Cisco XR

<details><summary>bgpq4 -X -l PL-ORION AS31257</summary>
<p>

```bash
no prefix-set PL-ORION
prefix-set PL-ORION
 80.65.16.0/20,
 80.65.16.0/21,
 80.65.16.0/24,
 80.65.20.0/24,
 80.65.24.0/21,
 81.25.76.0/22,
 81.25.76.0/23,
 81.25.76.0/24,
 81.25.77.0/24,
 81.25.78.0/23,
 81.25.78.0/24,
 81.25.79.0/24,
 86.62.6.0/24,
 94.73.192.0/18,
 94.73.192.0/19,
 94.73.202.0/24,
 94.73.224.0/19,
 94.73.240.0/20,
 94.73.240.0/24,
 94.73.241.0/24,
 94.73.242.0/24,
 94.73.243.0/24,
 94.73.244.0/24,
 94.73.245.0/24,
 94.73.246.0/24,
 94.73.247.0/24,
 94.73.248.0/24,
 94.73.249.0/24,
 94.73.250.0/24,
 94.73.251.0/24,
 94.73.252.0/24,
 109.226.192.0/18,
 109.226.192.0/19,
 109.226.224.0/19,
 134.19.128.0/21,
 171.33.248.0/21,
 171.33.250.0/23,
 171.33.252.0/23,
 176.116.160.0/20,
 176.116.160.0/21,
 176.116.168.0/21,
 185.84.172.0/23,
 185.84.172.0/24,
 185.84.173.0/24
end-set
```
</p>
</details>

Juniper

<details><summary>bgpq4 -J -l PL-ORION AS31257</summary>
<p>

```bash

policy-options {
replace:
 prefix-list PL-ORION {
    80.65.16.0/20;
    80.65.16.0/21;
    80.65.16.0/24;
    80.65.20.0/24;
    80.65.24.0/21;
    81.25.76.0/22;
    81.25.76.0/23;
    81.25.76.0/24;
    81.25.77.0/24;
    81.25.78.0/23;
    81.25.78.0/24;
    81.25.79.0/24;
    86.62.6.0/24;
    94.73.192.0/18;
    94.73.192.0/19;
    94.73.202.0/24;
    94.73.224.0/19;
    94.73.240.0/20;
    94.73.240.0/24;
    94.73.241.0/24;
    94.73.242.0/24;
    94.73.243.0/24;
    94.73.244.0/24;
    94.73.245.0/24;
    94.73.246.0/24;
    94.73.247.0/24;
    94.73.248.0/24;
    94.73.249.0/24;
    94.73.250.0/24;
    94.73.251.0/24;
    94.73.252.0/24;
    109.226.192.0/18;
    109.226.192.0/19;
    109.226.224.0/19;
    134.19.128.0/21;
    171.33.248.0/21;
    171.33.250.0/23;
    171.33.252.0/23;
    176.116.160.0/20;
    176.116.160.0/21;
    176.116.168.0/21;
    185.84.172.0/23;
    185.84.172.0/24;
    185.84.173.0/24;
 }
}
```
</p>
</details>

Huawei

<details><summary>bgpq4 -U -l PL-ORION AS31257</summary>
<p>

```bash
undo ip ip-prefix PL-ORION
ip ip-prefix PL-ORION permit 80.65.16.0 20
ip ip-prefix PL-ORION permit 80.65.16.0 21
ip ip-prefix PL-ORION permit 80.65.16.0 24
ip ip-prefix PL-ORION permit 80.65.20.0 24
ip ip-prefix PL-ORION permit 80.65.24.0 21
ip ip-prefix PL-ORION permit 81.25.76.0 22
ip ip-prefix PL-ORION permit 81.25.76.0 23
ip ip-prefix PL-ORION permit 81.25.76.0 24
ip ip-prefix PL-ORION permit 81.25.77.0 24
ip ip-prefix PL-ORION permit 81.25.78.0 23
ip ip-prefix PL-ORION permit 81.25.78.0 24
ip ip-prefix PL-ORION permit 81.25.79.0 24
ip ip-prefix PL-ORION permit 86.62.6.0 24
ip ip-prefix PL-ORION permit 94.73.192.0 18
ip ip-prefix PL-ORION permit 94.73.192.0 19
ip ip-prefix PL-ORION permit 94.73.202.0 24
ip ip-prefix PL-ORION permit 94.73.224.0 19
ip ip-prefix PL-ORION permit 94.73.240.0 20
ip ip-prefix PL-ORION permit 94.73.240.0 24
ip ip-prefix PL-ORION permit 94.73.241.0 24
ip ip-prefix PL-ORION permit 94.73.242.0 24
ip ip-prefix PL-ORION permit 94.73.243.0 24
ip ip-prefix PL-ORION permit 94.73.244.0 24
ip ip-prefix PL-ORION permit 94.73.245.0 24
ip ip-prefix PL-ORION permit 94.73.246.0 24
ip ip-prefix PL-ORION permit 94.73.247.0 24
ip ip-prefix PL-ORION permit 94.73.248.0 24
ip ip-prefix PL-ORION permit 94.73.249.0 24
ip ip-prefix PL-ORION permit 94.73.250.0 24
ip ip-prefix PL-ORION permit 94.73.251.0 24
ip ip-prefix PL-ORION permit 94.73.252.0 24
ip ip-prefix PL-ORION permit 109.226.192.0 18
ip ip-prefix PL-ORION permit 109.226.192.0 19
ip ip-prefix PL-ORION permit 109.226.224.0 19
ip ip-prefix PL-ORION permit 134.19.128.0 21
ip ip-prefix PL-ORION permit 171.33.248.0 21
ip ip-prefix PL-ORION permit 171.33.250.0 23
ip ip-prefix PL-ORION permit 171.33.252.0 23
ip ip-prefix PL-ORION permit 176.116.160.0 20
ip ip-prefix PL-ORION permit 176.116.160.0 21
ip ip-prefix PL-ORION permit 176.116.168.0 21
ip ip-prefix PL-ORION permit 185.84.172.0 23
ip ip-prefix PL-ORION permit 185.84.172.0 24
ip ip-prefix PL-ORION permit 185.84.173.0 24
```
</p>
</details>


```bash
# Просмотр инфы по таблице натирования для ip адреса x.x.x.x

conntrack -L | grep x.x.x.x 
```

```bash
# резолв симвлического адреса с трассировкой 

"dig +trace символический_адрес адрес_DNS_сервера"
```


Пример резолва адреса lenta.ru через DNS сервер 80.65.16.1

<details><summary>dig +trace lenta.ru 80.65.16.1</summary>
<p>

```bash
; <<>> DiG 9.11.5-P4-5.1+deb10u5-Debian <<>> +trace lenta.ru 80.65.16.1
;; global options: +cmd
.                       335512  IN      NS      l.root-servers.net.
.                       335512  IN      NS      j.root-servers.net.
.                       335512  IN      NS      m.root-servers.net.
.                       335512  IN      NS      f.root-servers.net.
.                       335512  IN      NS      h.root-servers.net.
.                       335512  IN      NS      g.root-servers.net.
.                       335512  IN      NS      a.root-servers.net.
.                       335512  IN      NS      d.root-servers.net.
.                       335512  IN      NS      c.root-servers.net.
.                       335512  IN      NS      b.root-servers.net.
.                       335512  IN      NS      i.root-servers.net.
.                       335512  IN      NS      e.root-servers.net.
.                       335512  IN      NS      k.root-servers.net.
.                       509232  IN      RRSIG   NS 8 0 518400 20210825050000 20210812040000 26838 . WrTvJW4fzqVP5/RTJkyowIZLXjuR9+IWtMhDwIEEEVaOHNNfPRo5pu9u f4OG1TicuWv+KU6orRNNaTBta3N/oAzPeVFu0cjbNRhCB9EI3nbFA2++ SYAEz2NrxZ7xJpnJ5oUxksvevOYe+SunFGO/Nl8j521Y+lQFRwyf0fRp MmIA+R2hyEu9LMZEl/EQHlg+s+PtIzuZIBpCWdJUN0L1hzgg1Aw5piUg YIqto+41PJ6b91nQqGw7ay2JVGP4LWBdQRr5qMB3xnPB7C9vmhXhpQIi cNYaAkYP2Gz+kS33N5WJ/XDGkAlmLCBJhKRGEyAxXV8bslgw0QggWmmV q6qKEg==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

ru.                     172800  IN      NS      a.dns.ripn.net.
ru.                     172800  IN      NS      b.dns.ripn.net.
ru.                     172800  IN      NS      d.dns.ripn.net.
ru.                     172800  IN      NS      e.dns.ripn.net.
ru.                     172800  IN      NS      f.dns.ripn.net.
ru.                     86400   IN      DS      7729 8 2 C035871113BB7C70C1FBFC1C9BFDFA9781733348D54F7EF9A4AD021A A00336F3
ru.                     86400   IN      RRSIG   DS 8 1 86400 20210825050000 20210812040000 26838 . nRXclAJcQi4vpoduZsGLMpFZo1XDgZhgoKnKbDH3HWiGTDcSAqmBwQVK clhJnntXwIp05lESc9+Ap7BSnjt8q4wydnZi5HjAW4pFOIckFdJVPGOc n/r6aANfdAF2z0w1mvC+sSrzS6aV62GYbL8j9eMdvebwP9LhZ0XxO45e 034dS/xEhEHbJ3DzIQgHtWpWLJ5pdjgamk3iyDhWQypc+qNcHH8JA+rR i0gGpxkgH2L3q4fx3N/w+6anrU1DSHXIgvXE8j9ra61abkc0+4VLldYL CaH/nxFhnTULNOoN4UTlDWyWiR3a+wWrpG1fIFoymUiKwOWIBzPFVTbq g7p/lQ==
;; Received 684 bytes from 199.7.91.13#53(d.root-servers.net) in 83 ms

lenta.ru.               345600  IN      NS      ns2.rambler.ru.
lenta.ru.               345600  IN      NS      ns3.rambler.ru.
lenta.ru.               345600  IN      NS      ns4.rambler.ru.
lenta.ru.               345600  IN      NS      ns5.rambler.ru.
tdui9d4jkuds8b9t86gj39pgflcnlgm5.ru. 3600 IN NSEC3 1 1 3 00FF TE33KGTAQU9M5CRQ3IBSILQU11GFGTTT NS SOA RRSIG DNSKEY NSEC3PARAM
tdui9d4jkuds8b9t86gj39pgflcnlgm5.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210905092702 20210731061836 19569 ru. EijlMV3UI2AWpv+n+mRzo/+DfY77anhN10Uyt2MKHCvduf8cxTrzSToU 6G309+HEMv356vI6IuBp+Dwlta1YjZ5XxlaV8PyJ8WNQaalqDqrUcT0b lk0lhVsWOCHq/alDfPEk2VLXw6i1+ixOwARWhEiqMiOS9gCuCM8ugNnU y+o=
cvdjk8qmto28dra6d6g6s2khntt6a6h9.ru. 3600 IN NSEC3 1 1 3 00FF CVLA5LV9LVJUN72AJD3Q3NNE28RPUHS1 NS DS RRSIG
cvdjk8qmto28dra6d6g6s2khntt6a6h9.ru. 3600 IN RRSIG NSEC3 8 2 3600 20210903151641 20210725101810 19569 ru. kfmOLJJEvCofnf1VN4nzV79GMvt5c3s/UsKyBbWo0D6ktdLsixw3SuFV DNJCev30LVpHy8Ndj9WJ7gJZhepEUmAhtFvwi++TNLIx3spC3vcURHeL t970EZIz7f5LIZkInGpmehLYGppJ0hQbVKoEM6h8d3fF1vwKsbw6OilP iMs=
;; Received 724 bytes from 194.85.252.62#53(b.dns.ripn.net) in 11 ms

lenta.ru.               600     IN      A       81.19.72.32
lenta.ru.               600     IN      A       81.19.72.58
lenta.ru.               600     IN      A       81.19.72.56
lenta.ru.               600     IN      A       81.19.72.33
lenta.ru.               600     IN      A       81.19.72.57
lenta.ru.               600     IN      A       81.19.72.59
lenta.ru.               600     IN      NS      ns4.rambler.ru.
lenta.ru.               600     IN      NS      ns2.rambler.ru.
lenta.ru.               600     IN      NS      ns5.rambler.ru.
lenta.ru.               600     IN      NS      ns3.rambler.ru.
;; Received 333 bytes from 2a02:6b0:6:2::2:53#53(ns4.rambler.ru) in 55 ms

.                       335511  IN      NS      j.root-servers.net.
.                       335511  IN      NS      b.root-servers.net.
.                       335511  IN      NS      g.root-servers.net.
.                       335511  IN      NS      l.root-servers.net.
.                       335511  IN      NS      m.root-servers.net.
.                       335511  IN      NS      i.root-servers.net.
.                       335511  IN      NS      c.root-servers.net.
.                       335511  IN      NS      a.root-servers.net.
.                       335511  IN      NS      d.root-servers.net.
.                       335511  IN      NS      k.root-servers.net.
.                       335511  IN      NS      e.root-servers.net.
.                       335511  IN      NS      h.root-servers.net.
.                       335511  IN      NS      f.root-servers.net.
.                       509231  IN      RRSIG   NS 8 0 518400 20210825050000 20210812040000 26838 . WrTvJW4fzqVP5/RTJkyowIZLXjuR9+IWtMhDwIEEEVaOHNNfPRo5pu9u f4OG1TicuWv+KU6orRNNaTBta3N/oAzPeVFu0cjbNRhCB9EI3nbFA2++ SYAEz2NrxZ7xJpnJ5oUxksvevOYe+SunFGO/Nl8j521Y+lQFRwyf0fRp MmIA+R2hyEu9LMZEl/EQHlg+s+PtIzuZIBpCWdJUN0L1hzgg1Aw5piUg YIqto+41PJ6b91nQqGw7ay2JVGP4LWBdQRr5qMB3xnPB7C9vmhXhpQIi cNYaAkYP2Gz+kS33N5WJ/XDGkAlmLCBJhKRGEyAxXV8bslgw0QggWmmV q6qKEg==
;; Received 1125 bytes from 80.65.16.1#53(80.65.16.1) in 0 ms

.                       86400   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2021081200 1800 900 604800 86400
.                       86400   IN      NSEC    aaa. NS SOA RRSIG NSEC DNSKEY
.                       86400   IN      RRSIG   SOA 8 0 86400 20210825050000 20210812040000 26838 . MVSk6dwXASaPCWDsGbpV4O3OfIUyruEf5p8IQ/KxKIOZjErjSPcxjTLI +lMpoWWclXT/iWSSDuIfxjfdlo52kMZG/2nm8iWs5tKEC9ut03Jy3Nnk 6SpDzd3cTTlAnfi+s/ENExIHcLTKj9qLplxVcUk25jHjuvAJhYl8Ti7g GP8WptJKV7L52sl2Xgs5I23UEpSArRPjzCqXeh9ZY3AtPk+p0IhRW4iI vV/KCCVzK/dRg+VdHoQyyaDWcInW7ZRM7I0Tv15o05j1xG1qL6ZwFeIi DNvvlqu5U8GRcSLDbHt/tz+YYAXdd0JrdIGjQ60P0bDmL3s43N+V+M39 kAIR7Q==
.                       86400   IN      RRSIG   NSEC 8 0 86400 20210825050000 20210812040000 26838 . dskPVWZdUnmOR2ioL3rjDal3whRdwqJOTw8Z4b1o0A3i1eNQB1S3lKsx 8OYNGPR0Xw5/sgddNlI//H6wffZJx0vvV15CSQVUViZfkO4wS72wknzz trpHPG7SHFYX2xMJ7RcIcaDISZxA1QPsAHZVdIOcD4JBmLpwbao5TwFP z7BrzR8Mk3aGpsWsEBzJuDm5Y8hIdN8u1U5bPQgVQT0wecXYDQb029dQ qK7dhu/Gl7PNJ8lTOuI4IxnSE6DHWTBVZ3CJga/OoY8jBgBFpZmOQRMR a0rBkhizln5RGpGb6fIgHTG1zomsY9s4PuhYPQc0keVoMZWvqmMSCuTC boK03A==
;; Received 711 bytes from 199.9.14.201#53(b.root-servers.net) in 94 ms

```
</p>
</details>


```bash
# посмотреть сетку без долбаного "dark blue" color
 ipcalc -n 109.226.192.0/18
```

whois

```bash
# узнать все маршруты, которые происходят из автономной системы.
whois -T route -i origin ASxxxxx 
```

Пример: список сетей закрепленных в RIPE за AS31257

<details><summary>whois -T route -i origin AS31257 | grep route</summary>
<p>

```bash
route:          109.226.192.0/18
route:          109.226.192.0/19
route:          109.226.224.0/19
route:          171.33.248.0/21
route:          171.33.250.0/23
route:          171.33.252.0/23
route:          176.116.160.0/20
route:          176.116.168.0/21
route:          185.84.172.0/23
route:          185.84.172.0/24
route:          80.65.16.0/20
route:          80.65.16.0/21
route:          80.65.24.0/21
route:          81.25.76.0/22
route:          81.25.76.0/23
route:          81.25.76.0/24
route:          81.25.77.0/24
route:          81.25.78.0/23
route:          81.25.78.0/24
route:          81.25.79.0/24
route:          86.62.6.0/24
route:          94.73.192.0/18
route:          94.73.192.0/19
route:          94.73.202.0/24
route:          94.73.224.0/19
route:          94.73.240.0/20
route:          94.73.240.0/24
route:          94.73.241.0/24
route:          94.73.242.0/24
route:          94.73.243.0/24
route:          94.73.244.0/24
route:          94.73.245.0/24
route:          94.73.246.0/24
route:          94.73.247.0/24
route:          94.73.248.0/24
route:          94.73.249.0/24
route:          94.73.250.0/24
route:          94.73.251.0/24
route:          94.73.252.0/24

```
</p>
</details>


```bash
# посмотреть шаблон объекта.
whois -t object_type 
```

Пример просмотра шаблона объекта "route"

<details><summary>whois -t route</summary>
<p>

```bash

% This is the RIPE Database query service.
% The objects are in RPSL format.
%
% The RIPE Database is subject to Terms and Conditions.
% See http://www.ripe.net/db/support/db-terms-conditions.pdf

route:          [mandatory]  [single]     [primary/lookup key]
descr:          [optional]   [multiple]   [ ]
origin:         [mandatory]  [single]     [primary/inverse key]
pingable:       [optional]   [multiple]   [ ]
ping-hdl:       [optional]   [multiple]   [inverse key]
holes:          [optional]   [multiple]   [ ]
org:            [optional]   [multiple]   [inverse key]
member-of:      [optional]   [multiple]   [inverse key]
inject:         [optional]   [multiple]   [ ]
aggr-mtd:       [optional]   [single]     [ ]
aggr-bndry:     [optional]   [single]     [ ]
export-comps:   [optional]   [single]     [ ]
components:     [optional]   [single]     [ ]
remarks:        [optional]   [multiple]   [ ]
notify:         [optional]   [multiple]   [inverse key]
mnt-lower:      [optional]   [multiple]   [inverse key]
mnt-routes:     [optional]   [multiple]   [inverse key]
mnt-by:         [mandatory]  [multiple]   [inverse key]
created:        [generated]  [single]     [ ]
last-modified:  [generated]  [single]     [ ]
source:         [mandatory]  [single]     [ ]

% This query was served by the RIPE Database Query Service version 1.101 (WAGYU)

```
</p>
</details>

```bash
#узнать все объекты, которые защищены объектом «maintainer_name».
whois -i mnt-by maintainer_name
```

```bash
#узнать, на какие более мелкие подсети поделена подсеть «subnet».
whois -M subnet 
```

Пример: узнать, на какие более мелкие подсети поделена подсеть 109.226.192.0/18

<details><summary>whois -M 109.226.192.0/18 | grep route</summary>
<p>

```bash


route:          109.226.192.0/19
route:          109.226.224.0/19
route:          109.226.247.0/24
route:          109.226.248.0/24
```
</p>
</details>


```bash
#узнать, из какой более крупной подсети образована подсеть «subnet».
whois -L subnet
```

Пример:  узнать, из какой более крупной подсети образована подсеть 109.226.192.0/19

<details><summary>whois -L 109.226.192.0/19 | grep route</summary>
<p>

```bash
route:          109.226.192.0/18
route:          109.226.192.0/19
```
</p>
</details>

## Linux terminal hot key
```bash
clear - Ctrl-L
```