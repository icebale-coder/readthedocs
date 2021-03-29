title: Lists
# Python работа со списком

## Работа со списком
Так же как со строками есть диапазоны, срезы шаги

```py
In [243]: list1 = [100, 200, 300,400, 500, 600, 700]

In [244]: list1
Out[244]: [100, 200, 300, 400, 500, 600, 700]

In [245]: list1[0]
Out[245]: 100

In [246]: list1[-1]
Out[246]: 700

In [247]: list1[1:5]
Out[247]: [200, 300, 400, 500]

In [248]: list1[:5]
Out[248]: [100, 200, 300, 400, 500]

In [249]: list1[:-1]
Out[249]: [100, 200, 300, 400, 500, 600]

In [250]: list1[::2]
Out[250]: [100, 300, 500, 700]

In [251]: list1[::-1]
Out[251]: [700, 600, 500, 400, 300, 200, 100]

```
## Список списков

```py
In [256]: mac_address_table = [['Gi2/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1'],
     ...:   ['Gi2/2', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2'],
     ...:  ['Gi2/3', '001a.4a16.010c', 'dynamic ip', 'GigabitEthernet2/3']]

In [257]: mac_address_table
Out[257]:
[['Gi2/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1'],
 ['Gi2/2', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2'],
 ['Gi2/3', '001a.4a16.010c', 'dynamic ip', 'GigabitEthernet2/3']]

#Тогда аналогично работы с одиночны списком получаем
In [275]: mac_address_table[0]
Out[275]: ['Gi2/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1']

In [276]: mac_address_table[1]
Out[276]: ['Gi2/2', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2']

In [277]: mac_address_table[0][1:2]
Out[277]: ['0000.5e00.0101']

In [278]: mac_address_table[0][0:2]
Out[278]: ['Gi2/1', '0000.5e00.0101']

#Обратное отображение элемнтов двойного списка
In [280]: mac_address_table[0][::-1]
Out[280]: ['GigabitEthernet2/1', 'dynamic ip', '0000.5e00.0101', 'Gi2/1']

```
