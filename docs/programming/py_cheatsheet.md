# Python cheatsheet

## Modules

### tabulate

Модуль позволяет строить таблицы на основе list'а tuple'ов

Пример использования:

``` python
from tabulate import tabulate

sh_ip_int_br = [('FastEthernet0/0', '15.0.15.1', 'up', 'up'), ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
                ('FastEthernet0/2', '10.0.13.1', 'up', 'up'), ('Loopback0', '10.1.1.1', 'up', 'up'),
                ('Loopback100', '100.0.0.1', 'up', 'up')]

print(tabulate(sh_ip_int_br))
    
---------------  ---------  --  --
FastEthernet0/0  15.0.15.1  up  up
FastEthernet0/1  10.0.12.1  up  up
FastEthernet0/2  10.0.13.1  up  up
Loopback0        10.1.1.1   up  up
Loopback100      100.0.0.1  up  up
---------------  ---------  --  --
```

#### headers

Этот параметр позволяет указывать заголовок формируемой таблицы

Его возможно указать как отдельный list:

``` python
from tabulate import tabulate

sh_ip_int_br = [('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
...: ('FastEthernet0/1', '10.0.12.1', 'up', 'up'),
...: ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
...: ('Loopback0', '10.1.1.1', 'up', 'up'),
...: ('Loopback100', '100.0.0.1', 'up', 'up')]
...:
columns = ['Interface', 'IP', 'Status', 'Protocol']

print(tabulate(sh_ip_int_br, headers=columns))

Interface        IP         Status    Protocol
---------------  ---------  --------  ----------
FastEthernet0/0  15.0.15.1  up        up
FastEthernet0/1  10.0.12.1  up        up
FastEthernet0/2  10.0.13.1  up        up
Loopback0        10.1.1.1   up        up
Loopback100      100.0.0.1  up        up
```

Или просто указать что первый ряд это заголовок:

```python
from tabulate import tabulate

sh_ip_int_br = [('Interface', 'IP', 'Status', 'Protocol'), ('FastEthernet0/0', '15.0.15.1', 'up', 'up'),
                ('FastEthernet0/1', '10.0.12.1', 'up', 'up'), ('FastEthernet0/2', '10.0.13.1', 'up', 'up'),
                ('Loopback0', '10.1.1.1', 'up', 'up'), ('Loopback100', '100.0.0.1', 'up', 'up')]

print(tabulate(sh_ip_int_br, headers='firstrow'))

```

Если данные приведены в виде списка из словарей, где заголовком являются ключи:

``` python
from tabulate import tabulate

sh_ip_int_br = [{'IP': '15.0.15.1',
                 'Interface': 'FastEthernet0/0',
                 'Protocol': 'up',
                 'Status': 'up'},
                {'IP': '10.0.12.1',
                 'Interface': 'FastEthernet0/1',
                 'Protocol': 'up',
                 'Status': 'up'},
                {'IP': '10.0.13.1',
                 'Interface': 'FastEthernet0/2',
                 'Protocol': 'up',
                 'Status': 'up'},
                {'IP': '10.1.1.1',
                 'Interface': 'Loopback0',
                 'Protocol': 'up',
                 'Status': 'up'},
                {'IP': '100.0.0.1',
                 'Interface': 'Loopback100',
                 'Protocol': 'up',
                 'Status': 'up'}]

print(tabulate(sh_ip_int_br, headers='keys'))
```

#### tablefmt

Формат grid:

```python
print(tabulate(sh_ip_int_br, headers='keys', tablefmt='grid'))

+-----------+-----------------+------------+----------+
| IP        | Interface       | Protocol   | Status   |
+===========+=================+============+==========+
| 15.0.15.1 | FastEthernet0/0 | up         | up       |
+-----------+-----------------+------------+----------+
| 10.0.12.1 | FastEthernet0/1 | up         | up       |
+-----------+-----------------+------------+----------+
| 10.0.13.1 | FastEthernet0/2 | up         | up       |
+-----------+-----------------+------------+----------+
| 10.1.1.1  | Loopback0       | up         | up       |
+-----------+-----------------+------------+----------+
| 100.0.0.1 | Loopback100     | up         | up       |
+-----------+-----------------+------------+----------+
```

Формат pipe, подойдёт для markdown:

``` python
print(tabulate(sh_ip_int_br, headers='keys', tablefmt='pipe'))

| IP        | Interface       | Protocol   | Status   |
|:----------|:----------------|:-----------|:---------|
| 15.0.15.1 | FastEthernet0/0 | up         | up       |
| 10.0.12.1 | FastEthernet0/1 | up         | up       |
| 10.0.13.1 | FastEthernet0/2 | up         | up       |
| 10.1.1.1  | Loopback0       | up         | up       |
| 100.0.0.1 | Loopback100     | up         | up       |
```

Формат HTML:

``` html
print(tabulate(sh_ip_int_br, headers='keys', tablefmt='html'))

<table>
<thead>
<tr><th>IP       </th><th>Interface      </th><th>Protocol  </th><th>Status  </th></tr>
</thead>
<tbody>
<tr><td>15.0.15.1</td><td>FastEthernet0/0</td><td>up        </td><td>up      </td></tr>
<tr><td>10.0.12.1</td><td>FastEthernet0/1</td><td>up        </td><td>up      </td></tr>
<tr><td>10.0.13.1</td><td>FastEthernet0/2</td><td>up        </td><td>up      </td></tr>
<tr><td>10.1.1.1 </td><td>Loopback0      </td><td>up        </td><td>up      </td></tr>
<tr><td>100.0.0.1</td><td>Loopback100    </td><td>up        </td><td>up      </td></tr>
</tbody>
</table>
```

#### stralign

Также возможно указать выравнивание столбцов:

``` python
print(tabulate(sh_ip_int_br, headers='keys', tablefmt='pipe', stralign='center'))

|    IP     |    Interface    |  Protocol  |  Status  |
|:---------:|:---------------:|:----------:|:--------:|
| 15.0.15.1 | FastEthernet0/0 |     up     |    up    |
| 10.0.12.1 | FastEthernet0/1 |     up     |    up    |
| 10.0.13.1 | FastEthernet0/2 |     up     |    up    |
| 10.1.1.1  |    Loopback0    |     up     |    up    |
| 100.0.0.1 |   Loopback100   |     up     |    up    |
```

#### Доки

https://bitbucket.org/astanin/python-tabulate

https://txt.arboreus.com/2013/03/13/pretty-print-tables-in-python.html

https://txt.arboreus.com/2013/12/12/tabulate-0-7-1-with-latex-tables-named-tuples-etc.html

### pprint

Этот модуль позволяет "красиво" выводить данные.

Например, он может изменить вывод словаря на более читаемый:

``` python
from pprint import pprint

london_co = {
    'r1': {'hostname': 'london_r1', 'location': '21 New Globe Walk', 'vendor': 'Cisco', 'model': '4451', 'IOS': '15.4',
           'IP': '10.255.0.1'},
    'r2': {'hostname': 'london_r2', 'location': '21 New Globe Walk', 'vendor': 'Cisco', 'model': '4451', 'IOS': '15.4',
           'IP': '10.255.0.2'},
    'sw1': {'hostname': 'london_sw1', 'location': '21 New Globe Walk', 'vendor': 'Cisco', 'model': '3850',
            'IOS': '3.6.XE', 'IP': '10.255.0.101'}}

pprint(london_co)

{'r1': {'IOS': '15.4',
        'IP': '10.255.0.1',
        'hostname': 'london_r1',
        'location': '21 New Globe Walk',
        'model': '4451',
        'vendor': 'Cisco'},
 'r2': {'IOS': '15.4',
        'IP': '10.255.0.2',
        'hostname': 'london_r2',
        'location': '21 New Globe Walk',
        'model': '4451',
        'vendor': 'Cisco'},
 'sw1': {'IOS': '3.6.XE',
         'IP': '10.255.0.101',
         'hostname': 'london_sw1',
         'location': '21 New Globe Walk',
         'model': '3850',
         'vendor': 'Cisco'}}
```

Или list'а:

```python
interfaces = [['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'], ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'], ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']]

[['FastEthernet0/0', '15.0.15.1', 'YES', 'manual', 'up', 'up'],
 ['FastEthernet0/1', '10.0.1.1', 'YES', 'manual', 'up', 'up'],
 ['FastEthernet0/2', '10.0.2.1', 'YES', 'manual', 'up', 'down']]
```

#### depth

Этот параметр позволяет ограничить вывод уровней вложенности

```python
result = {
    'interface Tunnel0': [' ip unnumbered Loopback0',
                          ' tunnel mode mpls traffic-eng',
                          ' tunnel destination 10.2.2.2',
                          ' tunnel mpls traffic-eng priority 7 7',
                          ' tunnel mpls traffic-eng bandwidth 5000',
                          ' tunnel mpls traffic-eng path-option 10 dynamic',
                          ' no routing dynamic'],
    'ip access-list standard LDP': [' deny 10.0.0.0 0.0.255.255',
                                    ' permit 10.0.0.0 0.255.255.255'],
    'router bgp 100': {' address-family vpnv4': [' neighbor 10.2.2.2 activate',
                                                 ' neighbor 10.2.2.2 send-community both',
                                                 ' exit-address-family'],
                       ' bgp bestpath igp-metric ignore': [],
                       ' bgp log-neighbor-changes': [],
                       ' neighbor 10.2.2.2 next-hop-self': [],
                       ' neighbor 10.2.2.2 remote-as 100': [],
                       ' neighbor 10.2.2.2 update-source Loopback0': [],
                       ' neighbor 10.4.4.4 remote-as 40': []},
    'router ospf 1': [' mpls ldp autoconfig area 0',
                      ' mpls traffic-eng router-id Loopback0',
                      ' mpls traffic-eng area 0',
                      ' network 10.0.0.0 0.255.255.255 area 0']}

pprint(result, depth=1)

{'interface Tunnel0': [...],
 'ip access-list standard LDP': [...],
 'router bgp 100': {...},
 'router ospf 1': [...]}

pprint(result, depth=2)

{'interface Tunnel0': [' ip unnumbered Loopback0',
                       ' tunnel mode mpls traffic-eng',
                       ' tunnel destination 10.2.2.2',
                       ' tunnel mpls traffic-eng priority 7 7',
                       ' tunnel mpls traffic-eng bandwidth 5000',
                       ' tunnel mpls traffic-eng path-option 10 dynamic',
                       ' no routing dynamic'],
 'ip access-list standard LDP': [' deny 10.0.0.0 0.0.255.255',
                                 ' permit 10.0.0.0 0.255.255.255'],
 'router bgp 100': {' address-family vpnv4': [...],
                    ' bgp bestpath igp-metric ignore': [],
                    ' bgp log-neighbor-changes': [],
                    ' neighbor 10.2.2.2 next-hop-self': [],
                    ' neighbor 10.2.2.2 remote-as 100': [],
                    ' neighbor 10.2.2.2 update-source Loopback0': [],
                    ' neighbor 10.4.4.4 remote-as 40': []},
 'router ospf 1': [' mpls ldp autoconfig area 0',
                   ' mpls traffic-eng router-id Loopback0',
                   ' mpls traffic-eng area 0',
                   ' network 10.0.0.0 0.255.255.255 area 0']}
```

#### pformat

Функция, которая отображает результат в виде строки

```python
print(pformat(result))
```

#### Доки

https://docs.python.org/3/library/pprint.html

https://pymotw.com/3/pprint/

### argparse

Этот модуль позволяет передавать аргументы при вызове .py скрипта. Пример использования:

```python
import subprocess
import argparse


def ping_ip(ip_address, count):
    """
    Ping IP address and return tuple:
    On success: (return code = 0, command output)
    On failure: (return code, error output (stderr))
    """
    reply = subprocess.run('ping -c {count} -n {ip}'.format(count=count, ip=ip_address), stdout=subprocess.PIPE,
                           stderr=subprocess.PIPE, encoding='UTF-8')
    if reply.returncode == 0:
        return True, reply.stdout
    else:
        return False, reply.stdout + reply.stderr


parser = argparse.ArgumentParser(description='Ping script')

parser.add_argument('-a', action="store", dest="ip")
parser.add_argument('-c', action="store", dest="count", default=2, type=int)

args = parser.parse_args()
print(args)

rc, message = ping_ip(args.ip, args.count)
print(message)
```

Чтобы создать элемент парсинга аргументов, нужно

```python
parser = argparse.ArgumentParser(description='Ping script')
```

Затем, чтобы добавить агрументы, нужно вызывать функцию у этого элемента:

```python
parser.add_argument('-a', action="store", dest="ip", required=True))
```

`action="store"` определяет то, что значение нужно сохранить, а `dest="ip"` определяет переменную, в которую сохранить. 

`required=True` говорит, что этот аргумент обязателен при выполнении скрипта, без него скрипт выведет:

```bash
usage: 12.0a.py [-h] -a IP [-c COUNT]
12.0a.py: error: the following arguments are required: -a
```

Также возможно заранее определять дефолтные значения этого агрумента, которые будут подставлены если аргумент не был определён, а также указывать тип принимаемого значения аргумента:

```python
parser.add_argument('-c', action="store", dest="count", default=2, type=int)
```

#### metavar

Ещё возможно определять имя аргумента при выводе в help:

```bash
parser.add_argument('-c', action="store", dest="count", default=2, type=int, metavar='Packet Count')

optional arguments:
  -h, --help       show this help message and exit
  -c Packet Count  Number of ICMP packets to send
```

#### nargs

Он позволяет указать количество элементов, которые попадут в этот аргумент. Есть несколько вариантов его значений:

* `N` - должно быть указанное количество аргументов. Аргументы будут в списке (даже если указан 1)

* `?` - 0 или 1 аргумент

* `*` - все аргументы попадут в список

* `+` - все аргументы попадут в список, но должен быть передан хотя бы один аргумент

```python
add_parser.add_argument('filename', nargs='+', help='file(s) to add to db')
```

Тогда help выглядит так:

```bash

```





#### help

argparse также автоматически формирует --help сообщения. По умолчанию они формируются автоматически:

```bash
C:\Users\ct7lh1\Google Диск\Study\Python\pyneng>py 12.0a.py -h
usage: 12.0a.py [-h] -a IP [-c COUNT]

Ping script

optional arguments:
  -h, --help  show this help message and exit
  -a IP
  -c COUNT
```

Чтобы задать кастомное описание для аргумента, необходимо у него дополнительно указать параметр help:

```python
parser.add_argument('-a', action="store", dest="ip", required=True, help="IP address or name to ping")
parser.add_argument('-c', action="store", dest="count", default=2, type=int, help="Number of ICMP packets to send")

C:\Users\ct7lh1\Google Диск\Study\Python\pyneng>py 12.0a.py -h
usage: 12.0a.py [-h] -a IP [-c COUNT]

Ping script

optional arguments:
  -h, --help  show this help message and exit
  -a IP       IP address or name to ping
  -c COUNT    Number of ICMP packets to send
```

#### Позиционные аргументы

Кроме указания самих агрументов можно задавать их позиционно (т.е. писать их надо будет в определенном порядке):

```python
parser.add_argument('host', action="store", help="IP address or name to ping")
```

При этом стоит знать, что это значение будет автоматически сохранено в переменной host и будет обязательным по умолчанию, поэтому, если тут указать параметры `dest=` или `required=`, то скрипт выведет исключение.

Также изменится вывод help:

```bash
C:\Users\ct7lh1\Google Диск\Study\Python\pyneng>py 12.0a.py -h
usage: 12.0a.py [-h] [-c COUNT] host

Ping script

positional arguments:
  host        IP address or name to ping

optional arguments:
  -h, --help  show this help message and exit
  -c COUNT    Number of ICMP packets to send
```

### RouterOS-API

Изначально необходимо установить соеднинение с удалённым роутером:

```python
import routeros_api

connect = routeros_api.RouterOsApiPool('IP_ADD', username='USER', password='PASSWORD')
api = connect.get_api()
```

Чтобы закрыть соединение:

```python
connect.disconnect()
```

#### Getting info

Затем, чтобы получить информацию о конфигурации какого-либо из контекстов, необходимо:

```python
list = api.get_resource('/COMMAND')
list_add.get()
```

Например, чтобы вывести список сконфигурированных IPшников нужно:

```python
list_add = api.get_resource('/ip/address')
print(list_add.get())

>> [{'id': '*1', 'address': '192.168.88.1/24', 'network': '192.168.88.0', 'interface': 'bridge', 'actual-interface': 'bridge', 'invalid': 'false', 'dynamic': 'false', 'disabled': 'false', 'comment': 'defconf'}]
```

#### Adding values

Чтобы добавить какое-либо значение в конфигурацию, необходимо:

```python
list = api.get_resource('/COMMAND')
list.add(attribute="VALUE", attribute_n="VALUE")
```

При этом нужно обратить внимание, что атрибуты с `-` меняют его на `_`, например `max-limit` становится `max_limit`.

Например, чтобы повешать IPшник на ether1, нужно:

```python
adding_ip = api.get_resource("/ip/address")
adding_ip.add(address="10.10.10.10/24", interface="ether1")
```

#### Setting values

Чтобы изменить какое-либо имеющееся значение, необходимо:

```python
list = api.get_resource('/COMMAND')
list.set(id, attribute)
```

Например, чтобы изменить хостнейм:

```python
set_hostname = api.get_resource('/system/identity')
set_hostname.set(name="raeuihu")
```

#### Removing elements

Чтобы удалить какое-либо значение, нужно:

```python
list = api.get_resource('/COMMAND')
list.remove(id)
```

Например, чтобы удалить IPшник ether1:

```python
delete_ip = api.get_resource("/ip/address")
delete_ip.remove(id="1")
```

