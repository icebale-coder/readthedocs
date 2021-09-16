title: Strings
# Python работа со строками 

## Присваивание значения

```py
	a = 'text11'
	b = "text22"

	print(a)
	text11

	print(b)
	text22
```

## Ввод длинных строк 

```py
	a = "line1\nline2\nline3"

	print(a)
	line1
	line2
	line3
```

```py
    a = '''interface Vlan1
    description << Vlan1 >>
    ip address 192.168.0.254 255.255.255.0
    ip access-group 1 in
    ip access-group 2 out
    no ip redirects
    no ip unreachables
    no ip proxy-arp
    no ipv6 redirects
    no ipv6 unreachables
    counter ipv4 ipv6 separate
    end'''

	print (a)
	interface Vlan1
	 description << Vlan1 >>
	 ip address 192.168.0.254 255.255.255.0
	 ip access-group 1 in
	 ip access-group 2 out
	 no ip redirects
	 no ip unreachables
	 no ip proxy-arp
	 no ipv6 redirects
	 no ipv6 unreachables
	 counter ipv4 ipv6 separate
	end
```

## Экранирование символов/спец. символы

```py
	a = 'tes\'t'
	b = "tes't"

	print(a)
	tes't

	print(b)
	tes't

```

```py

	a = "tes\"t"
	b = 'tes"t'

	print(a)
	tes"t

	print(b)
	tes"t

```

## Обращение к элементам строки, срезы, шаг

К строке можно обрашаться по номеру элемента, делать срезы, устанавливать шаг

```py
#Первоначальная строка
In [62]: a = "interface Vlan1"

#В python элементы строки начинается с 0
#последний элемент строки имеет индекс -1

#Вывод 0 элемента строки.
In [63]: print(a[0])
i

#Вывод 1 элемента строки.
In [64]: print(a[1])
n

#Вывод последнего элемента строки.
In [65]: print(a[-1])
1

#Вывод предпоследнего элемента строки.
In [66]: print(a[-2])
n

#Срезы строки
#Вывод среза строки с 0 по 9 элемент

In [67]: print(a[0:9])
interface

#Можно опускать начало и конец среза 
#тогда пустые значения будут означать 
#начало и конец строки соовтетственно

#Срез от начала строки дл 9 элемента
In [68]: print(a[:9])
interface

#Срез с 10 элемента до конца строки
In [69]: print(a[10:])
Vlan1

#Срез от 5 элемента с концадо конца строки
In [70]: print(a[-5:])
Vlan1

#Срез с начала строки до предпоследнего символа
In [71]: print(a[0:-1])
interface Vlan

#Срез с начала строки до конца строки
In [72]: print(a[:])
interface Vlan1

#Срез с начала строки до конца строки
In [73]: print(a[0:])
interface Vlan1


#Шаг  
#За основу работы с шагом возьмем срез  от начала строки до 9 элемента
In [74]: print(a[0:9])
interface

#Выбор из среза каждого второго элемента
In [75]: print(a[0:9:2])
itrae

#Выбор из среза каждого третьего элемента
In [76]: print(a[0:9:3])
iea

#При указании шага -1 
#Получаем перевернутую строку
In [77]: print(a[::-1])
1nalV ecafretni

```
## Методы для работы со строками, функции

```py
In [62]: a = "interface Vlan1"

#Вывести в верхнем регистре
In [85]: a.upper()
Out[85]: 'INTERFACE VLAN1'

#Вывести в нижнем регистре
In [86]: a.lower()
Out[86]: 'interface vlan1'

#Поменять регистры
In [87]: a.swapcase()
Out[87]: 'INTERFACE vLAN1'

#Вывести строку все с маленькой буквы, кроме первой
In [88]: a.capitalize()
Out[88]: 'Interface vlan1'

#Подсчет количества вхождений подстроки
# буквы 'e' (подстроки 'e')
In [96]: a.count('e')
Out[96]: 2

# подстроки 'int'
In [97]: a.count('int')
Out[97]: 0

# подстроки 'Int'
In [98]: a.count('Int')
Out[98]: 1


#Длина строки =15
In [106]: a = 'Interface vlan1'

#Метод count() возврашает на +1 больше 
In [107]: a.count('')
Out[107]: 16

#Функция len - точно
In [108]: len(a)
Out[108]: 15


#Поиск первого вхождения подстроки
In [109]: a.find('a')
Out[109]: 6
#Если сделаем срез с 6ого элемента, то как раз первый #элемент этого среза и будет символ 'a'
#Но второе вхождение никак не учитывается
In [110]: a[6:]
Out[110]: 'ace vlan1'

#Проверка с чего начинается, заканчивается строка
In [111]: a.startswith('I')
Out[111]: True

In [112]: a.startswith('i')
Out[112]: False

In [113]: a.endswith('vlan1')
Out[113]: True

In [114]: a.endswith('Vlan1')
Out[114]: False

#Замена части строки
In [115]: a.replace('vlan1','Vlan10')
Out[115]: 'Interface Vlan10'

```

### Избавление от спец символов.
```py
#Часто в выводе команд встречаются спец символы типа табуляций
#перехода на нову строку и пр...

In [121]:  a = '\t\tInterface vlan1\n\t'

In [122]: a
Out[122]: '\t\tInterface vlan1\n\t'

In [123]: print(a)
                Interface vlan1

#Чтобы избавиться от спец смволов

#strip() - удаление спец символов изо всей строки
In [127]: a.strip()
Out[127]: 'Interface vlan1'

#strip() - удаление спец символов только справа
In [128]: a.rstrip()
Out[128]: '\t\tInterface vlan1'

#strip() - удаление спец символов только слева
In [129]: a.lstrip()
Out[129]: 'Interface vlan1\n\t'


#strip - Может удалять символы справа и слева
#!но не может всередине

In [148]: a = '[110/80]'

In [149]: a
Out[149]: '[110/80]'

#Удаляем справа и слева символы []
In [150]: a.strip('[]')
Out[150]: '110/80'

#Аналогична пред. команде
#Удаляем справа и слева символы ][
In [151]: a.strip('][')
Out[151]: '110/80'

#Если символ в середине, то не получится
#Удаляем справа и слева символы /
In [152]: a.strip('/')
Out[152]: '[110/80]'

#А вот такая конструкция отработает нормально
#Удаляем справа и слева символы ]1[
In [153]: a.strip(']1[')
Out[153]: '0/80'

```

### Получение из строки списка 
```py

#Получение из строки списка 
#по дефолту строкой разделителем является 
#силволы whitespace 
In [156]:  a = '\t\tInterface vlan1\n\t'

In [157]: a.split()
Out[157]: ['Interface', 'vlan1']

In [158]:  a = '\t\tInterface \t vlan1\n\t'

In [159]: a.split()
Out[159]: ['Interface', 'vlan1']


In [160]:  a = '1,2,3,4,5'

In [161]: a.split(',')
Out[161]: ['1', '2', '3', '4', '5']


#Можно в явном виде указывать разделительный символ 
In [160]:  a = '1,2,3,4,5'

#тогда при указании запятой в качестве разделителя 
#получим список такого вида
In [161]: a.split(',')
Out[161]: ['1', '2', '3', '4', '5']


#split() - дефолтовый умный  - он удвляет все символы whitespac-ов
In [169]: a = '\t\tInterface \t vlan1\n\t'

In [170]: print(a)
                Interface        vlan1


In [171]: a.split()
Out[171]: ['Interface', 'vlan1']

#В отличие от общего
In [172]: a.split(' ')
Out[172]: ['\t\tInterface', '\t', 'vlan1\n\t']
```

### Форматирование строк

при помощи форматирования строк можно сделать шаблон
и заполнять его согласно 

```py
#Например, при помощи форматирования строк 
#можно сделать шаблон для интер фейсов
#и заполнять его согласно списку интерфейсов

In [181]: intf_tmpl ='interface {}'

In [182]: intf_tmpl.format('g0/1')
Out[182]: 'interface g0/1'


In [183]: intf_tmpl.format('eth1/48')
Out[183]: 'interface eth1/48'


# можно использовать переменную напрямую
# при работе с format
In [185]: f = 'gi0/0'

In [186]: intf_tmpl.format(f)
Out[186]: 'interface gi0/0'

#По сути выполняется вот такая конструкуция
In [184]: 'interface {}'.format('eth1/48')
Out[184]: 'interface eth1/48'
```

#### Форматирование нескольких строк

```py
# Создаем шаблон
In [202]: intf_tmpl = "{}{}{}"

# Определяем переменные 
In [203]: intf = 'gi0/10'
In [204]: vlan = 10
In [205]: mac = '0000.deed.beef'

#Выводим в форматированном виде
In [206]: intf_tmpl.format(intf, vlan, mac)
Out[206]: 'gi0/10100000.deed.beef'

# Т.к. все поля шаблона "слиплись"
# Можно задать фиксрованную длину каждого поля шалона
# Соответственно фиксированной длины 7, 5 и 15
In [209]: intf_tmpl = "{:7}{:5}{:15}"
# В итоге фаблон отформатирует строки уже по фиксированной длине
In [210]: intf_tmpl.format(intf, vlan, mac)
Out[210]: 'gi0/10    100000.deed.beef '

# Т.к переменная Vlan - имеет тип interger, 
# то она по умолчанию форматируется справа
# Чтобы исправить это можно принудительно указать "край" форматирования
In [211]: intf_tmpl = "{:7}{:<5}{:15}"

# Тогда шаблон выглядет нормально
In [212]: intf_tmpl.format(intf, vlan, mac)
Out[212]: 'gi0/10 10   0000.deed.beef '

# И в результате строки форматируются ровно по столбцам
In [213]: intf = 'gi0/1'
In [214]: intf_tmpl.format(intf, vlan, mac)
Out[214]: 'gi0/1  10   0000.deed.beef '
```

```py
# Можно рассмотреть такой вариант шаблона
In [220]: intf_tmpl = '''
     ...: interface:  {}
     ...: vlan:       {}
     ...: mac:        {}
     ...: '''

# Тогда
In [221]: intf_tmpl.format(intf, vlan, mac)
Out[221]: '\ninterface:  gi0/1\nvlan:       10\nmac:        0000.deed.beef\n'

# В print это выглядет 
In [222]: print(intf_tmpl.format(intf, vlan, mac))

interface:  gi0/1
vlan:       10
mac:        0000.deed.beef
```

```py
# Еще вариант использования форматированного шаблона
In [241]: intf_tmpl = '''
     ...: Название интерфейса {intf}
     ...: interface:  {intf}
     ...: vlan:       {vlan}
     ...: mac:        {mac}
     ...: '''

In [242]: print (intf_tmpl.format(intf='gi0/0/0', vlan='10', mac='0000.dead.beef'))

Название интерфейса gi0/0/0
interface:  gi0/0/0
vlan:       10
mac:        0000.dead.beef

```

#### Конветирование систем при помощи форматирования:

```py
# Преобразование 10ой цифры в двоичный вид
In [227]: '{:b}'.format(255)
Out[227]: '11111111'

In [228]: '{:b}.{:b}{:b}{:b}'.format(192, 168, 0, 1)
Out[228]: '11000000.1010100001'


In [229]: '{:b}'.format(255)
Out[229]: '11111111'

In [230]: '{:b}.{:b}.{:b}.{:b}'.format(192, 168, 0, 1)
Out[230]: '11000000.10101000.0.1'

# Чтобы двоичными знаков было 8 зададим от 0 до 8 
In [231]: '{:08b}.{:08b}.{:08b}.{:08b}'.format(192, 168, 0, 1)

# Тогда получится вот в таком виде
Out[231]: '11000000.10101000.00000000.00000001'

```

