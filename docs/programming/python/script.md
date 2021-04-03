title: script's base

# Python базовая работа со скриптами

## Написание имени файла (правила хорошего "тона")
Обычно файл скрипта пишется мелкими буквами с расширением py
Например **example1.py**

## Запуск скрипта
python example1.py

## Содержание скрипта example1.py

```py

port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений
print('\n'.join(port).format('Port1', 166))

```

## Запуск скрипта example1.py
```bash
python ./example1.py

description << Port1 >>
switchport mode trunk
switchport trunk allowed vlan 166
spanning-tree bpdufilter enable

```

## Запуск скрипта автоматически example1.py
```bash
Для этого необходимо:
1. Назначить права на запуск файла в Linux
chmod 755 ./example1.py

python@debian: $ ls -la
total 12
drwxr-xr-x  2 python python 4096 Apr  3 02:10 .
drwxr-xr-x 30 python python 4096 Apr  3 02:00 ..
-rwxr-xr-x  1 python python  406 Apr  3 02:10 example1.py

2. Указать в скрипте команду запуска питона строку шебанга (shebang):
#!/usr/bin/env python3

Т.е. выглядеть скрипт тогда будет так
python@debian: $ cat ./example1.py
#!/usr/bin/env python3

port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений

print('\n'.join(port).format('Port1', 166))
```

!!! note "shebang(шебанг) - #!"
	шебанг - это последовательность из двух символов '#!'. Загрузчик программ рассматривает остаток строки после шебанга как имя файла программы-интерпретатора. Загрузчик запускает эту программу и передаёт ей в качестве параметра имя файла скрипта с шебангом.

инфа о [#!](https://ru.wikipedia.org/wiki/%D0%A8%D0%B5%D0%B1%D0%B0%D0%BD%D0%B3_(Unix))

## Передача аргументов скрипту

пример: example1.py 'port1',100

пример: example1.py 'port2',200

```bash
Чтобы скрипт работал с агрументами для вывода данных нужно
импортировать модуль sys, в нем есть список argv
В списке argv хранятся имя файла и параметры(аргуметы),
указанный в командной строке при запуске

import sys
print(sys.argv)

Тогда example1.py с аргументами будет возвращать 
./example1.py port11 23
['./example1.py', 'port11', '23']

```

Изменяем соответственно скрипт 
```bash
#!/usr/bin/env python3

import sys

port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений

print('\n'.join(port).format(sys.argv[1], sys.argv[2]))
```
И тогда будет возвращаться вывод в соответствии с заполненными аргументами
```bash
./example1.py 'port 1' '11,15,22'

['./example1.py', 'port 1', '11,15,22']
description << port 1 >>
switchport mode trunk
switchport trunk allowed vlan 11,15,22
spanning-tree bpdufilter enable
```
Вариация на тему - можно импортировать не весь мождуль "sys", а только argv из него

тогда
```bash
python@debian: $ cat ./example2.py
#!/usr/bin/env python3

from sys import argv

print(argv)
port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений

print('\n'.join(port).format(argv[1], argv[2]))

```
По итогу будет тоже самое 
```bash
./example2.py p1 2
['./example2.py', 'p1', '2']
description << p1 >>
switchport mode trunk
switchport trunk allowed vlan 2
spanning-tree bpdufilter enable
```

## Распаковка в переменные
```py
In [1]: args = ['port1', '100']

In [2]: port, vlan = args

In [3]: port
Out[3]: 'port1'

In [4]: vlan
Out[4]: '100'
```
Тогда
```py
cat ./example2.py
#!/usr/bin/env python3

from sys import argv

desc, vlan = argv[1:3]

port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений

print('\n'.join(port).format(desc, vlan))

```

При запуске 
```bash
python@debian: $ ./example2.py p1 22
description << p1 >>
switchport mode trunk
switchport trunk allowed vlan 22
spanning-tree bpdufilter enable
```

!!! warning "При запуске, если количество аргументов не соответствует, то будет ошибка"
```bash
./example2.py p1
Traceback (most recent call last):
  File "./example2.py", line 6, in <module>
    desc, vlan = argv[1:3]
ValueError: not enough values to unpack (expected 2, got 1)
```

## Ввод информации от пользователя input() 
```py

In [7]: user = input('Username: ')
Username: alex

In [8]: user
Out[8]: 'alex'

```
### Создадим скрипт с возможностью интерактивного воода данных от пользователя
```py
cat ./example3.py
#!/usr/bin/env python3

desc= input('Введите описание порта: ')
vlan= input('Введите номер влана: ')

port = ['description << {} >>',
'switchport mode trunk',
'switchport trunk allowed vlan {}',
'spanning-tree bpdufilter enable']

# преобразование элементов списка "port" в строку,
# разделенную переводом на след.строку '\n', с подстановкой в темплейты значений

print('\n'.join(port).format(desc, vlan))

```
### Результат запуска такого скрипта
```bash
./example3.py
Введите описание порта: collocate server
Введите номер влана: 111
description << collocate server >>
switchport mode trunk
switchport trunk allowed vlan 111
spanning-tree bpdufilter enable

```
