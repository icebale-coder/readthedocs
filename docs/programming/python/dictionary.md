title: Dictionary

# Python работа со словорями

## Определение и удобство словаря
!!! note "Словарь являются изменяемым типом данных"

```bash
Определение: словарь состоит из выражения в фигурных скобках 
пар ключ-значение 
{'ключ1': 'значение1','ключ2': 'значение2'}
Удобство в том, что значение элемента словаря это не индекс, а имя
```

```bash
#Справнение словаря со списком
# Создадим список
In [449]: list1 = ['R1','1.1.1.1','15.1']

#К элементам списка можно обращаться только по индексу
In [450]: list1[0]
Out[450]: 'R1'

In [451]: list1[2]
Out[451]: '15.1'


#Создадим словарь
In [453]: dict1 = {'hostname': 'R1',
     ...:         'lo0': '1.1.1.1',
     ...:         'ver': '15.1'}

#К элементам словаря можно обращаться по имени
In [455]: dict1['lo0']
Out[455]: '1.1.1.1'

In [456]: dict1['ver']
Out[456]: '15.1'
```

## Добавление данных в словарь

```bash
In [459]: dict1['vlan'] = '10'

In [460]: dict1
Out[460]: {'hostname': 'R1', 'lo0': '1.1.1.1', 'ver': '15.1', 'vlan': '10'}


In [462]: dict1['vlan']
Out[462]: '10'
```

## Изменение данных в словаре

Перезапись данных в словаре

```bash
In [467]: dict1['vlan']=11

In [468]: dict1
Out[468]: {'hostname': 'R1', 'lo0': '1.1.1.1', 'ver': '15.1', 'vlan': 11}

In [469]: dict1['vlan']
Out[469]: 11
```

## Удаление данных из словаря

```bash
# Через опреатор del
In [472]: del dict1['lo0']

In [473]: dict1
Out[473]: {'hostname': 'R1', 'ver': '15.1', 'vlan': 11}

```

## Словарь словарей
```bash
In [484]: krk_filial = {
     ...: 'router1' : {'hostname': 'R1', 'ver': '15.1', 'vlan': 11, 'lo1': '1.1.1.1'},
     ...: 'router2' : {'hostname': 'R2', 'ver': '15.2', 'vlan': 12, 'lo2': '2.2.2.2'},
     ...: 'router3' : {'hostname': 'R3', 'ver': '15.3', 'vlan': 13, 'lo3': '3.3.3.3'}
     ...: }

In [485]: krk_filial
Out[485]:
{'router1': {'hostname': 'R1', 'ver': '15.1', 'vlan': 11, 'lo1': '1.1.1.1'},
 'router2': {'hostname': 'R2', 'ver': '15.2', 'vlan': 12, 'lo2': '2.2.2.2'},
 'router3': {'hostname': 'R3', 'ver': '15.3', 'vlan': 13, 'lo3': '3.3.3.3'}}
     

In [486]: krk_filial['router1']
Out[486]: {'hostname': 'R1', 'ver': '15.1', 'vlan': 11, 'lo1': '1.1.1.1'}

In [487]: krk_filial['router1']['hostname']
Out[487]: 'R1'

```