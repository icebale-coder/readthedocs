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

