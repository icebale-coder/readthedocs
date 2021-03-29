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
## Именения значений списка и присвоение списка
```py
#Меняем значение элдемнета в списке
In [299]: mac_address_table[0][0]='Gi2/1/1'

In [300]: mac_address_table
Out[300]:
[['Gi2/1/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1'],
 ['Gi2/2', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2'],
 ['Gi2/3', '001a.4a16.010c', 'dynamic ip', 'GigabitEthernet2/3']]

# Присваиваем список другому списку
In [301]: fdb1 = mac_address_table

# id при этом у них одинаковое
# Это значит, что по сути данные списки указываю 
# на одну и ту же область памяти
In [302]: id(mac_address_table)
Out[302]: 140236437906248

In [303]: id(fdb1)
Out[303]: 140236437906248

In [304]: fdb1
Out[304]:
[['Gi2/1/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1'],
 ['Gi2/2', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2'],
 ['Gi2/3', '001a.4a16.010c', 'dynamic ip', 'GigabitEthernet2/3']]

# При изменение значения в одном таком списке следует, что и спорой список 
# поменяет значение, т.к. это одна и та же обласить памяти
In [305]: mac_address_table[1][0]='Gi2/2/1'

In [306]: fdb1
Out[306]:
[['Gi2/1/1', '0000.5e00.0101', 'dynamic ip', 'GigabitEthernet2/1'],
 ['Gi2/2/1', '001a.4a16.010b', 'dynamic ip', 'GigabitEthernet2/2'],
 ['Gi2/3', '001a.4a16.010c', 'dynamic ip', 'GigabitEthernet2/3']]

```
## Создание копии списка
```py
# Создадим список
In [340]: fdb1= [1,2,3]
# Присвоим его другому списку
In [341]: fdb2 = fdb1

In [343]: fdb1
Out[343]: [1, 2, 3]

In [344]: fdb2
Out[344]: [1, 2, 3]


# Чтобы сделать новую копию независимую от существующей
# надо использовать copy(), тогда выделяется новая область памяти, 
# куда копируются значения списка
In [342]: fdb3 = fdb1.copy()

In [345]: fdb3
Out[345]: [1, 2, 3]

# Из вывода видно, что fdb1 и fdb2 ссылаются 
# на одну и ту же область памяти
# а fdb3 на другую

In [346]: id(fdb1)
Out[346]: 140236437784520

In [347]: id(fdb2)
Out[347]: 140236437784520

In [348]: id(fdb3)
Out[348]: 140236438565128

# соответственно при изменении данных в fdb1
In [349]: fdb1[0] = 10

In [350]: fdb1
Out[350]: [10, 2, 3]

# данные в fdb2 меняются
In [351]: fdb2
Out[351]: [10, 2, 3]

# А в fdb3 остаются прежними
In [352]: fdb3
Out[352]: [1, 2, 3]

```


