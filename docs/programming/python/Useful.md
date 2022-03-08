title: Useful (comprehentions, zip, lyamda)

# Полезные функции и пр.

## (list,dist,set) comprehensions
"Генераторы" списков, словарей, множеств.


```bash
"Задача: вывести на экран список состоящий из численных целых элементов"
In [56]: vlans = [10, 20, 40, 50, 60]                  

# при объединении такого списка в строку будет выаваться ошибка, т.к. нельзя преобразовать напяямую число в строку.
In [57]: ", ".join(vlans)
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-57-18847b333370> in <module>
----> 1 ", ".join(vlans)
TypeError: sequence item 0: expected str instance, int found

# для решения данной задачи можно использовать генератор списка, 
# в котором будут уже преобразованные с строку элементы

In [58]: vlans_str = [str(v) for v in vlans]            

In [59]: ", ".join(vlans_str)                               
Out[59]: '10, 20, 40, 50, 60'

# более укороченный варинт "паровозиком"
In [60]: ", ".join([str(v) for v in vlans]}       
Out[60]: '10, 20, 40, 50, 60'

#По сути выщеописанная генерация это укорченный вариант такой конструкции
In [62]: vlans_str = [] 
    ...: for v in vlans: 
    ...:     vlans_str.append(str(v)) 

In [63]: vlans_str                                                                                                                                                                                    Out[63]: ['10', '20', '40', '50', '60']

```


## zip
Позволяет упаковывать несколько данных в одну структуру.

```bash
"Пример создание списка словарей из структуры данных: заголовок + список списков"

In [26]: list1 = [["gi0/1", "10.0.1.1", "duplex", "up"], ["gi0/2", "10.0.1.2", "auto", "up"], ["gi0/3", "10.0.1.3", "duplex", "down"]]                                                                                                        

In [27]: header = ["interface", "ip", "physic", "status"]                                                                                                                                                                                     

In [28]: dict_intf = [dict(zip(header,l)) for l in list1]                                                                                                                                                                                     

In [29]: dict_intf                                                                                                                                                                                                                            
Out[29]: 
[{'interface': 'gi0/1', 'ip': '10.0.1.1', 'physic': 'duplex', 'status': 'up'},
 {'interface': 'gi0/2', 'ip': '10.0.1.2', 'physic': 'auto', 'status': 'up'},
 {'interface': 'gi0/3', 'ip': '10.0.1.3', 'physic': 'duplex', 'status': 'down'}
]

#В структуре типа список словарей удобно обращаться к однотипным данным по ключу.
# Например получить все ip адреса на интерфейсах

In [30]: for intf in dict_intf: 
    ...:     print(intf["ip"]) 
    ...:                                                                                                                                                                                                                                      
10.0.1.1
10.0.1.2
10.0.1.3

```


