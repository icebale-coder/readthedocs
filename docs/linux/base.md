title: Base commands

## Полезные команды/программы

```bash
# Просмотр инфы по таблице натирования для ip адреса x.x.x.x

conntrack -L | grep x.x.x.x 
```

```bash
# посмотреть сетку без долбаного "dark blue" color
 ipcalc -n 109.226.192.0/18
```

whois
```bash
# узнать все маршруты, которые происходят из автономной системы.
whois -T route -i origin ASxxxxx 


# Пример: список сетей закрепленных в RIPE за AS31257
whois -T route -i origin AS31257 | grep route

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

```bash
# посмотреть шаблон объекта.
whois -t object_type 


# Пример просмотра шаблона объекта "route"

whois -t route
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

```bash
#узнать все объекты, которые защищены объектом «maintainer_name».
whois -i mnt-by maintainer_name
```

```bash
#узнать, на какие более мелкие подсети поделена подсеть «subnet».
whois -M subnet 

#Пример: узнать, на какие более мелкие подсети поделена подсеть 109.226.192.0/18
whois -M 109.226.192.0/18 | grep route
route:          109.226.192.0/19
route:          109.226.224.0/19
route:          109.226.247.0/24
route:          109.226.248.0/24

```

```bash
#узнать, из какой более крупной подсети образована подсеть «subnet».
whois -L subnet

# Пример:  узнать, из какой более крупной подсети образована подсеть 109.226.192.0/19
whois -L 109.226.192.0/19 | grep route
route:          109.226.192.0/18
route:          109.226.192.0/19

```

## Linux terminal hot key
```bash
clear - Ctrl-L
```