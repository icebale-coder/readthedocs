title: Strings
# Python работа со строками 

## Присваивание значения

```py
	a = 'text1'
	b = "text1"

	print(a)
	tes't

	print(b)
	tes't
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

#Можно опускать начало и конец среза тогда пустые значения будут означать начало и конец строки соовтетственно

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

#Выбор из среза каждого второго эемента
In [75]: print(a[0:9:2])
itrae

#Выбор из среза каждого третьего эемента
In [76]: print(a[0:9:3])
iea

#При указании шага -1 
#Получаем перевернутую строку
In [77]: print(a[::-1])
1nalV ecafretni

```
