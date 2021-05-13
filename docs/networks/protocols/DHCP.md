title: DHCP

# Протокол DHCP

## Назначение
Протокол DHCP(Dynamic Host Configuration Protocol) - предназначен для автоматической передачи ip настроек по запросу клиента.
Работает по модели "клиент-сервер", используется в качестве транспортного протока [UPD](https://icebale.readthedocs.io/en/latest/networks/protocols/UDP/).

Клиент отправляет запросы с 68 порта, порт сервера 67.

## Структура сообщения DHCP

![image-dhcp-struct](img/dhcp-structure.jpg)


| Field       | Description                                      | Length   |
| ----------- | ------------------------------------------------ | -------- |
|op           | Тип сообщения. Boot Request = 1, Boot Reply = 2  | 1 byte   |
|htype	      | Hardware Type - аппаратный тип - Ethernet = 1 	 | 1 byte   |
|hlen	      | Длина аппаратного адреса. MAC-адреса Ethernet = 6| 1 byte   |


-op	 - Тип сообщения. Boot Request = 1, Boot Reply = 2 - 
- htype	Тип аппаратного адреса. Допустимые значения этого поля определены в RFC 1700 «Assigned Numbers». Например, для MAC-адреса Ethernet это поле принимает значение 0x01.	1
hlen	Длина аппаратного адреса в байтах. Для MAC-адреса Ethernet —0x06.	1
hops	Количество промежуточных маршрутизаторов (так называемых агентов ретрансляции DHCP), через которые прошло сообщение. Клиент устанавливает это поле в 0x00.	1
xid	Уникальный идентификатор транзакции в 4 байта, генерируемый клиентом в начале процесса получения адреса.	4
secs	Время в секундах с момента начала процесса получения адреса. Может не использоваться (в этом случае оно устанавливается в 0x0000).	2
flags	Поле для флагов — специальных параметров протокола DHCP.	2
ciaddr	IP-адрес клиента. Заполняется только в том случае, если клиент уже имеет собственный IP-адрес и способен отвечать на запросы ARP (это возможно, если клиент выполняет процедуру обновления адреса по истечении срока аренды).	4
yiaddr	Новый IP-адрес клиента, предложенный сервером.	4
siaddr	IP-адрес сервера. Возвращается в предложении DHCP (см. ниже).	4
giaddr	IP-адрес агента ретрансляции, если таковой участвовал в процессе доставки сообщения DHCP до сервера.	4
chaddr	Аппаратный адрес (обычно MAC-адрес) клиента.	16
sname	Необязательное имя сервера в виде нуль-терминированной строки.	64
file	Необязательное имя файла на сервере, используемое бездисковыми рабочими станциями при удалённой загрузке. Как и sname, представлено в виде нуль-терминированной строки.	128
options	Поле опций DHCP. Здесь указываются различные дополнительные параметры конфигурации. В начале этого поля указываются четыре особых байта со значениями 99, 130, 83, 99 («волшебные числа»), позволяющие серверу определить наличие этого поля. Поле имеет переменную длину, однако DHCP-клиент должен быть готов принять DHCP-сообщение длиной в 576 байт (в этом сообщении поле options имеет длину 340 байт).


## Процесс получения DHCP адреса
Процесс получения ip адреса по DHCP представляет из себя четыре стадии.
Его еще называют "DORA" - по первым буквам стадий.

### 1. DHCP Discover 
Клиент отправляет в сеть широковещательный запрос на поиск DHCP сервера о том, что он хочет получить настройки ip. 

![image-dhcp-discover](img/dhcp-1-discover.jpg)

### 2. DHCP Offer 
Сервер отправляет юнисастом, клиенту предложение с вариантом настроек ip. 
При этом сервер выделяет эти адреса временно, п.э. нет точной гарантии, что клиент их получит.

![image-dhcp-offer](img/dhcp-2-offer.jpg)

### 3. DHCP Request 
Клиент отправляет широковещательный запрос на сервер DHCP, 
таким образом подтверждая своё согласие принять данные настройки от DHCP сервера.

![image-dhcp-request](img/dhcp-3-request.jpg)

### 4. DHCP Ack
Сервер принимает DHCP Request от клиента и если данный адрес еще никому не назначился, 
то отправляет подтверждение в виде DHCP Ack.

![image-dhcp-ack](img/dhcp-4-ack.jpg)

После этого клиент начинает работать с заданными ip параметрами, 
а сервер помечает данный ip адрес занятым данным клиентам.

Возможен вариант когда dhcp сервер может отвергнуть подтверждение DHCP Request.
тогда он ответит сообщением DHCP NAK  

## Время аренды (lease time)



## Режимы работы протокола

### Клиент ит сервер подключены напрямую

![image-dhcp-dora](img/dhcp-dora.jpg)

### Клиент и сервер подключены через dhcp relay

![image-dhcp-relay-dora](img/dhcp-relay-dora.jpg)


Образцы дампов:

- [1. Стандартый DHCP DORA](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/dhcp-dora.pcapng)
- [2. DHCP DORA Renew](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/dhcp-dora-renew.pcapng)
- [3. DHCP Release, Nak, Inform](https://icebale.readthedocs.io/en/latest/networks/wireshark.collection/dhcp-release-nak-inform.pcapng)

Литература:


[1. Принципы работы протокола DHCP](https://selectel.ru/blog/dhcp-protocol/#:~:text=DHCP%20%E2%80%94%20%D0%BF%D1%80%D0%BE%D1%82%D0%BE%D0%BA%D0%BE%D0%BB%20%D0%BF%D1%80%D0%B8%D0%BA%D0%BB%D0%B0%D0%B4%D0%BD%D0%BE%D0%B3%D0%BE%20%D1%83%D1%80%D0%BE%D0%B2%D0%BD%D1%8F%20%D0%BC%D0%BE%D0%B4%D0%B5%D0%BB%D0%B8,%D0%B5%D1%81%D1%82%D1%8C%20%D0%BA%D0%BE%D0%BC%D0%BF%D1%8C%D1%8E%D1%82%D0%B5%D1%80%D1%83%20%D0%B2%20%D0%BB%D0%BE%D0%BA%D0%B0%D0%BB%D1%8C%D0%BD%D0%BE%D0%B9%20%D1%81%D0%B5%D1%82%D0%B8.)

[2. How a DHCP Server Allocates Network Parameters to New DHCP Clients](https://support.huawei.com/enterprise/en/doc/EDOC1100116724/5cef90ad/how-a-dhcp-server-allocates-network-parameters-to-new-dhcp-clients)

[3. Principles of DHCPv4 Address Allocation](https://support.huawei.com/enterprise/en/doc/EDOC1100125886/c5ff6555/principles-of-dhcpv4-address-allocation)