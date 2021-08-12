title: Base commands

# Полезные команды

```bash
#Просмотр инфы по таблице натирования для ip адреса x.x.x.x

conntrack -L | grep x.x.x.x 
```

whois
```bash

#Просмотр объектов типа "route", принадлежащих AS31257
whois -T route -i origin AS31257 


#Пример: список сетей закрепленных в RIPE за AS31257
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


# Linux terminal hot key
```bash
clear - Ctrl-L
```