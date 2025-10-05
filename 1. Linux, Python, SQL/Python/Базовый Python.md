<h2>Базовый Python</h2>
<h3>1. Введение</h3>

Python — это высокоуровневый, интерпретируемый язык программирования с динамической типизацией и автоматическим управлением памятью. Он известен своей читаемостью кода и поддерживает множество парадигм программирования, включая процедурную, объектно-ориентированную и функциональную.

Особенности синтаксиса Python:

- Отступы: В Python блоки кода разделяются отступами, что делает код легко читаемым, причем вложенность инструкций определяется кратностью выбранных отступов (то есть, если мы выбрали 4 пробела в качестве отступа, то следующий уровень вложенности кода будет иметь отступ в 8 пробелов). Для улучшения читаемости кода советуется выбирать отступы с более широким прыжком (к примеру, tab является более лучшей альтернативой отступу в один пробел).
- Конец строки: В Python нет необходимости ставить точку с запятой, так как конец строки считается в том числе и концом инструкции. Запись инструкции можно делить на несколько строк, в таком случае, всю конструкцию необходимо заключать в круглые, квадратные либо фигурные скобки. Также допускается запись нескольких инструкций в одной строке, разделенных точкой с запятой, однако такой способ считается нежелательным в виду ухудшения читаемости кода.
- Комментарии: Строки, начинающиеся с #, считаются комментариями и игнорируются интерпретатором. Также возможно использование блокового обрамления в случае, если комментарии состоят из нескольких предложений. Такие комментарии начинаются и заканчиваются тройками символов """.
- Именование переменных: Имена переменных могут содержать буквы, цифры и подчеркивания, но не могут начинаться с цифры.
- Вложенные инструкции: Разделяются двоеточием

<h3>2. Объявление переменных, операторы и выражения</h3>
Переменные в Python не требуют явного объявления для резервирования места в памяти. Объявление происходит автоматически при присваивании значения переменной. Python является языком с динамической типизацией, что означает, что тип переменной определяется в момент присваивания ей значения.

Пример объявления переменных в Python:

```python
x = 5               # Целочисленный тип данных (int)
y = 3.14            # Число с плавающей точкой (float)
name = "Alice"      # Строка (string)
is_valid = True     # Булев тип (bool)
```
Операторы в Python делятся на несколько типов:
- Арифметические операторы:
	- Простые операторы сложения, вычитания, умножения и деления: +, -, *, /
	- Модуль (остаток от деления): %
	- Целочисленное деление: //
	- Возведение в степень: **
- Операторы сравнения: ==, !=, >, <, >=, <=.
- Логические операторы: and, or, not
- Строковые операторы
	- Конкатенация: +
	- Повторение: *

Пример использования операторов:
```python
a = 10
b = 3
print(a + b)  # Сложение, выводит 13
print(a - b)  # Вычитание, выводит 7
print(a * b)  # Умножение, выводит 30
print(a / b)  # Деление, выводит 3.333...
print(a % b)  # Модуль, выводит 1
print(a // b) # Целочисленное деление, выводит 3
print(a ** b) # Возведение в степень, выводит 1000
print(a == b)  # False, потому что a не равно b
print(a != b)  # True, потому что a не равно b
print(a < b)   # False, потому что a не меньше b
print(a > b)   # True, потому что a больше b

a = True
b = False
print(a and b)  # False, потому что оба выражения должны быть True
print(a or b)   # True, потому что хотя бы одно выражение True
print(not a)    # False, потому что a True

name = "Alice"
greeting = "Hello, " + name + "!"
print(greeting)  # Выводит "Hello, Alice!"

laugh = "Ha" * 3
print(laugh)     # Выводит "HaHaHa"
```

<h3>3. Типы данных</h3>

Python поддерживает различные типы данных:
- Числовые типы:
	- Целые числа (int):
	Допускается преобразование строк, вещественных чисел (с отбрасыванием дробной части) и чисел, записанных в разных системах счисления, в целое число с применением функции int().
```python
x = 10
print(type(x))                    # Вывод: <class 'int'>
print(int(' -12_3'))              # Вывод: -123
print(int(0b101010)               # Вывод: 42
print(int('0b101010', base=2))    # Вывод: 42
print(int('  0o177 ', base=8))    # Вывод: 127
```
	- Числа с плавающей точкой (float):
	Допускается преобразование строк, целых чисел и чисел, записанных в разных системах счисления, в целое число с применением функции float().
```python
y = 20.5
print(type(y))                 # Вывод: <class 'float'>
print(float('.500  '))         # Вывод: 0.5
print(23_421_233.000_000_1)    # Вывод: 23421233.0000001
float('nan')                   # Вывод: nan, аналогично: inf, -inf
```
	- Комплексные числа (complex):
	Класс complex позволяет как объявить комплексную переменную, так и осуществить преобразование любого целочисленного или вещественного числа, а также числа записанного в виде строки, в комплексное. Не допускается обратное преобразование комплексного числа в целочисленное или вещественное с применением функций int() или float().
```python
z = 2 + 3j
print(type(z))    # Вывод: <class 'complex'>
print(z.real)     # Вывод: 2.0
print(z.imag)     # Вывод: 3.0
```
- Булев тип:
Булев тип данных (bool) может принимать одно из двух значений: True или False.
```python
is_active = True
print(type(is_active))  # Вывод: <class 'bool'>
```
- Строковый тип:
```python
print("This is a 'sample' text")
print('This is a "sample" text')
print('''This is a 'sample' text with a "combination"''')
```
Подстановка значений в строки:
```python
import datetime
name = 'Борис'
age = 20
anniversary = datetime.date(2004, 01, 01)
print(f'Меня зовут {name}, в следующем году мне исполнится {age+1}, дата моего рождения: {anniversary:%A, %B %d, %Y}.')	
# Вывод: 'Меня зовут Борис, в следующем году мне исполнится 21, дата моего рождения: Thursday, January 1, 2004.' 
def sayMyName(name):
      return name
f'result={sayMyName(name)}'	 # Вывод: 'result=Борис'
```
Конкатенация:
```python
print('This is a ' "'sample'" + ' concatenated text with a' ''' 'strange' "combination"''')  	# Вывод: This is a 'sample' concatenated text with a 'strange' "combination"
str_arr = ['Каждый',  'охотник',  'желает', 'знать', 'где', 'сидит', 'фазан']
line = ' => '.join(str_arr)
print(line)  	# Вывод: Каждый => охотник => желает => знать => где => сидит => фазан
```
Сравнение:
```python
print("apple" > "ananas")      # True, так как ord("p") > ord("n")
print("apple" > "Apple")       # True, так как ord("a") > ord("A"), сравнение регистрозависимо
print("apple" > "apple123")    # False
```

Методы строк:
 - `str.upper()`: возвращает строку в верхнем регистре.
 - `str.lower()`: возвращает строку в нижнем регистре.
 - `str.capitalize()`: возвращает строку с первым символом в верхнем регистре, остальные в нижнем.
 - `str.title()`: возвращает строку, в которой каждое слово начинается с заглавной буквы.
 - `str.strip()`: удаляет пробелы в начале и конце строки.
 - `str.lstrip()`: удаляет пробелы в начале строки.
 - `str.rstrip()`: удаляет пробелы в конце строки.
 - `str.replace(old, new)`: заменяет все вхождения подстроки old на new.
 - `str.split(sep)`: разбивает строку на список подстрок по разделителю sep.
 - `str.join(iterable)`: объединяет элементы iterable (например, список) в строку, используя строку как разделитель.
 - `str.find(sub)`: возвращает индекс первого вхождения подстроки sub или -1, если подстрока не найдена.
 - `str.index(sub)`: аналогично find, но вызывает исключение, если подстрока не найдена.
 - `str.startswith(prefix)`: проверяет, начинается ли строка с prefix.
 - `str.endswith(suffix)`: проверяет, заканчивается ли строка на suffix.
 - `str.isdigit()`: проверяет, состоит ли строка только из цифр.
 - `str.isalpha()`: проверяет, состоит ли строка только из букв.
 - `str.isalnum()`: проверяет, состоит ли строка только из букв и цифр.
 - `str.islower()`: проверяет, все ли символы строки в нижнем регистре.
 - `str.isupper()`: проверяет, все ли символы строки в верхнем регистре.

Проверить переменную на соответствие типу данных можно с помощью `isinstance`:
```python
a = 23
b = 2.3
c = "apple"
print(isinstance(a, int))             # True
print(isinstance(b, (int, float)))    # True
print(isinstance(c, (int, float)))    # False
```

<h3>4. Базовые коллекции</h3>

В Python существует несколько базовых типов коллекций, таких как строки, списки, кортежи, словари и множества.
- **Строки**, **списки** и **кортежи** поддерживают операции индексации и срезов для доступа к элементам.
- **Словари** предоставляют возможность быстрого доступа к данным по ключу.
- **Множества** используются для операций над множествами, таких как объединение, пересечение и разность.

1. **Список (list)** - упорядоченная изменяемая коллекция объектов различных типов:
```python
fruits = ['apple', 'banana', 'cherry', 'date']
print(type(fruits))           # Вывод: <class 'list'>
print(fruits[0])              # Вывод: apple
print(fruits[-1])             # Вывод: date
fruits.append('pineapple')    # Добавление элемента в конец списка
# Срезы
print(fruits[1:3])   # Вывод: ['banana', 'cherry']
print(fruits[:3])    # Вывод: ['apple', 'banana', 'cherry']
print(fruits[1:])    # Вывод: ['banana', 'cherry', 'date', 'pineapple']
print(fruits[::2])   # Вывод: ['apple', 'cherry', 'pineapple']
print(fruits[::-1])  # Вывод: ['pineapple', 'date', 'cherry', 'banana', 'apple']
# Методы
fruits.extend([4,5])          # Присоединение списка в конец
fruits.insert(7, 6)           # Добавление элемента на индекс
fruits.count(4)               # Число вхождений элемента
fruits.index(4)               # Индекс первого вхождения
fruits.remove(4)              # Удалить первое вхождение
fruits.pop(6)                 # Удалить элемент по индексу и вернуть его
fruits.reverse()              # Развернуть список
fruits.sort()                 # Отсортировать список
```
По аналогии со списками, к элементам строки также можно обращаться по индексу, а также получать срез:
```python
name = "Python"
print(name[0])  # Вывод: 'P'
print(name[1:4])  # Вывод: 'yth'
```

2. **Кортеж (tuple)** - упорядоченная неизменяемая коллекция объектов различных типов:
Часто используются для:
 - Возвращения нескольких значений из функции. 
 - Хранения данных, которые не должны изменяться, например, координаты точек или записи из БД.
 - Прохода в циклах, особенно при работе с словарями.

 ```python
my_tuple = (1, "Hello", 3.14)
print(type(my_tuple ))    # Вывод: <class 'tuple'>
print(my_tuple)           # Вывод: (1, 'Hello', 3.14)
print(my_tuple[0])        # Вывод: 1
print(my_tuple[1])        # Вывод: Hello
print(my_tuple[1:])       # Вывод: ('Hello', 3.14)
# Конкатенация
another_tuple = (42, "World")
combined_tuple = my_tuple + another_tuple
print(combined_tuple)    # Вывод: (1, 'Hello', 3.14, 42, 'World')
repeated_tuple = my_tuple * 2
print(repeated_tuple)    # Вывод: (1, 'Hello', 3.14, 1, 'Hello', 3.14)
# Распаковка
a, b, c = my_tuple
print(a)    # Вывод: 1
print(b)    # Вывод: Hello
print(c)    # Вывод: 3.14
```

3. **Словарь (dict)** - упорядоченная коллекция пар ключ-значение:
```python
my_dict = {"name": "John", "age": 30, "city": "New York"}
print(type(my_dict))                     # Вывод: <class 'dict'>
print(my_dict)                           # Вывод: {'name': 'John', 'age': 30, 'city': 'New York'}
print(my_dict["name"])                   # Вывод: John
print(my_dict.get("birthday"))           # Вывод: None
print(my_dict["birthday"])               # Ошибка
# Редактирование
my_dict["age"] = 31                      # Изменение значения
my_dict["email"] = "john@example.com"    # Добавление нового ключа-значения
del my_dict["city"]                      # Удаление элемента с использованием del
my_dict.pop("email")                     # Удаление элемента с возвращением значения
my_dict.update({"email": "john@example.com"})    # Добавление одного или нескольких значений
```
Получение элементов словаря:
```python
person = {'name': 'Alice', 'age': 25, 'city': 'Moscow'}
# Ключи
keys_obj = person.keys()
print(keys_obj)                 # dict_keys(['name', 'age', 'city'])
print(type(keys_obj))           # <class 'dict_keys'>
# Значения
values_obj = person.values()
print(values_obj)               # dict_values(['Alice', '25', 'Moscow'])
print(type(values_obj))         # <class 'dict_values'>
# Пары
items_obj = person.items()
print(items_obj)                # dict_items([('name', 'Alice'), ('age', '25'), ('city', 'Moscow')])
print(type(items_obj))          # <class 'dict_items'>
```
Создание из списков с помощью zip:
```python
keys = ["name", "age", "city"]
values = ["Alice", 25, "Moscow"]
pairs = list(zip(keys, keys))    # [('name', 'Alice'), ('age', '25'), ('city', 'Moscow')]
person = dict(pairs)             # {'name': 'Alice', 'age': 25, 'city': 'Moscow'}
```

4. **Множество (set)** - неупорядоченная коллекция уникальных элементов:
```python
my_set = {1, 2, 3}
print(type(my_set))                     # Вывод: <class 'set'>
print(my_set)                           # Вывод: {1, 2, 3}
my_set2 = set([2, 3, 4])                # Множество из списка
print(my_set2)                          # Вывод: {2, 3, 4}
immutable_set = frozenset([1, 2, 3])    # Неизменяемое множество
print(immutable_set)                    # Вывод: frozenset({1, 2, 3})
# Добавление
my_set.add(4)               # Добавление одного элемента
my_set.update([5, 6])       # Добавление нескольких элементов
# Удаление
my_set.remove(6)            # Удаление элемента, ошибка если отсутствует
my_set.discard(5)           # Удаление элемента без ошибки
my_set.pop()                # Удаление и возврат произвольного элемента множества
# Операции над множествами
union_set = my_set.union(my_set2)                                  # Объединение множеств
print(union_set)                                                   # Вывод: {1, 2, 3, 4}
intersection_set = my_set.intersection(my_set2)                    # Пересечение множеств
print(intersection_set)                                            # Вывод: {2, 3, 4}
difference_set = my_set.difference(my_set2)                        # Разность множеств
print(difference_set)                                              # Вывод: {1}
symmetric_difference_set = my_set.symmetric_difference(my_set2)    # Симметрическая разность
print(symmetric_difference_set)                                    # Вывод: {1}
```

<h3>5. Коллекции модуля collections</h3>

Модуль `collections` предоставляет специализированные типы данных-контейнеры, которые являются альтернативами встроенным контейнерам (таким как `list`,` dict`, `set`, `tuple`). Эти специализированные типы данных полезны в различных сценариях и могут сделать код более эффективным и читаемым.
1. **namedtuple** - кортеж с именованными полями:
```python
from collections import namedtuple
Point = namedtuple('Point', ['x', 'y'])    # Создание namedtuple
p = Point(10, 20)
print(p.x, p.y)      # 10 20
print(p[0], p[1])    # 10 20 (доступ по индексу тоже работает)
# Пример с данными
Person = namedtuple('Person', ['name', 'age', 'city'])
person1 = Person('Alice', 25, 'Moscow')
print(f"{person1.name} from {person1.city}")
```
2. **deque** - двусторонняя очередь:
```python
from collections import deque
dq = deque([1, 2, 3])    # Создание deque
print(dq)                # deque([1, 2, 3])
# Добавление элементов
dq.append(4)        # в конец
dq.appendleft(0)    # в начало
print(dq)           # deque([0, 1, 2, 3, 4])
# Удаление элементов
last = dq.pop()         # с конца
first = dq.popleft()    # с начала
print(f"Удален: {first}, осталось: {dq}")
# Ограничение длины
limited_dq = deque(maxlen=3)
for i in range(5):
    limited_dq.append(i)
    print(limited_dq)  # Выводит последние 3 элемента
```
3. **Counter** - счетчик для хэшируемых объектов:
```python
from collections import Counter
# Подсчет элементов в списке
words = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple']
word_count = Counter(words)
print(word_count)  # Counter({'apple': 3, 'banana': 2, 'orange': 1})
# Работа со строками
text = "абракодабра"
char_count = Counter(text)
print(char_count.most_common(3))  # 3 самых частых символа
# Математические операции
c1 = Counter(a=3, b=2)
c2 = Counter(a=1, b=2, c=1)
print(c1 + c2)   # Counter({'a': 4, 'b': 4, 'c': 1})
print(c1 - c2)   # Counter({'a': 2})
```
3. **OrderedDict** - упорядоченный словарь с дополнительными методами:
```python
from collections import OrderedDict
od = OrderedDict()
od['z'] = 1
od['a'] = 2
od['c'] = 3
print(list(od.keys()))             # ['z', 'a', 'c']
od.move_to_end('z')                # Перемещение элемента в конец
print(list(od.keys()))             # ['a', 'c', 'z']
od.move_to_end('a', last=False)    # Перемещение элемента в начало
print(list(od.keys()))             # ['a', 'c', 'z']
```

Оптимизация памяти при работе с коллекциями:
- Использование кортежей вместо списков
```python
import sys
list_data = [1, 2, 3, 4, 5]
tuple_data = (1, 2, 3, 4, 5)
print(sys.getsizeof(list_data))     # 104
print(sys.getsizeof(tuple_data))    # 80
```
- Использование генераторов вместо списков:
```python
import random
def memory_intensive_operation():
    numbers = [random.randint(1, 1000) for _ in range(1000000)]
    total = sum(numbers)
    return total
def memory_efficient_operation():
    numbers = (random.randint(1, 1000) for _ in range(1000000))
    total = sum(numbers)
    return total
```
- Использование array вместо списков для числовых данных:
```python
import sys
import array
numbers_list = [1, 2, 3, 4, 5] * 1000
numbers_array = array.array('i', [1, 2, 3, 4, 5] * 1000)
# Экономия становится значительной для больших объемов данных
print(sys.getsizeof(numbers_list))     # 40056
print(sys.getsizeof(numbers_array))    # 20080
```

<h3>6. Управляющие конструкции</h3>

1. Условия (if, elif, else):
```python
if условие1:
    # блок кода, выполняемый, если условие1 истинно
elif условие2:
    # блок кода, выполняемый, если условие1 ложно, но условие2 истинно
else:
    # блок кода, выполняемый, если условие1 и условие2 ложны
```
2. Циклы (for, while):
```python
# Цикл for
for переменная in последовательность:
		# блок кода для выполнения
else:
		# блок кода после цикла
# Цикл while
while условие:
		# блок кода для выполнения
else:
		# блок кода после цикла
```
`break` - выход из текущего цикла
`continue` - переход на следующую итерацию текущего цикла
`pass` - заглушка
3. Обработчик исключений (try, except, else, finally):
```python
number = int(input("Введите число: "))
try:
		result = 10 / number               # Код в котором возможна ошибка
except ValueError:
		print("Это не число!")             # Обработка ValueError
except ZeroDivisionError:
		print("Нельзя делить на ноль!")    # Обработка ZeroDivisionError
else:
		print(f"Результат: {result}")      # Выполняется, если исключений не было
finally:
		print("Завершение операции")       # Выполняется всегда
```

<h3>7. Включения (comprehensions)</h3>
Включения (comprehensions) — это специальные конструкции в Python, которые позволяют компактно создавать коллекции данных.

1. Списковые включения (list comprehensions):
```python
squares = [x**2 for x in range(10)]                         # Вывод: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
even_squares = [x**2 for x in range(10) if x % 2 == 0]      # Вывод: [0, 4, 16, 36, 64]
values = [x if x > 0 else 0 for x in [-1, 2, -3, 4]]        # Вывод: [0, 2, 0, 4]
```
2. Словарные включения (dict comprehensions):
```python
even_squares = {x: x**2 for x in range(7) if x % 2 == 0}               # Вывод: {2: 4, 4: 16, 6: 36}
words = ['apple', 'banana', 'cherry']
word_lengths = {word: len(word) for word in words if len(word) > 5}    # Вывод: {'banana': 6, 'cherry': 6}
```
3. Множественные включения (set comprehensions):
```python
even_squares_set = {x**2 for x in range(10) if x**2 % 2 == 0}          # Вывод: {0, 4, 16, 36, 64}
```

<h3>8. Импорт модулей и пакетов</h3>

```python
import math                                        # Обычный импорт
print(math.sqrt(25))                               # Вывод: 5.0
print(math.pi)                                     # Вывод: 3.141592653589793

import math as m                                   # Импорт с псевдонимом
print(m.sqrt(16))                                  # Вывод: 4.0

from math import sqrt, pi                          # Импорт конкретных объектов
print(sqrt(9))                                     # Вывод: 3

from my_package import math_utils, string_utils    # Импорт пакета (папки с модулями)
print(string_utils.reverse_string("123"))          # 321
```

<h3>9. Функции и их аргументы</h3>
Функции определяются с помощью ключевого слова def:

```python
def function_name(parameters):
    # Тело функции
    return result
```
Параметры функции – это переменные в определении функции. Аргументы функции – фактические значения, передаваемые в функцию. Аргументы функций бывают позиционными (порядок передачи важен) и именованными (порядок передачи может быть любым). args в определении функции используется для передачи произвольного количества позиционных аргументов, а kwargs – для передачи произвольного количества именованных. Порядок аргументов в определении функции: обычные аргументы, args, аргументы по умолчанию, kwargs.
Пример:
```python
def complex_function(a, b, *args, option = True, **kwargs):
    print(f"a: {a}, b: {b}")
    print(f"args: {args}")
    print(f"option: {option}")
    print(f"kwargs: {kwargs}")

complex_function(1, 2, 3, 4, 5, option = False, name = "Анна", age = 25):
# Вывод:
# a: 1, b: 2
# args: (3, 4, 5)
# option: False
# kwargs: {"name": "Анна", "age": 25}
```
Также можно применять аннотирование типов:
```python
def my_sum(a: int, b: int) -> int:
    return a + b
```

<h3>10. Lambda-функции, map, filter, sorted, reduce</h3>

Lambda-функции — это анонимные функции, которые определяются с помощью ключевого слова lambda. Они могут принимать любое количество аргументов, но содержат только одно выражение:
```python
square = lambda x: x ** 2          # Простая lambda-функция
print(square(5))                   # 25
multiply = lambda x, y: x * y      # Lambda с несколькими аргументами
print(multiply(3, 4))              # 12
greet = lambda: "Hello, World!"    # Lambda без аргументов
print(greet())                     # Hello, World!
```
`map` - применяет функцию к каждому элементу итерируемого объекта:
```python
numbers = [1, 2, 3, 4, 5]
string_numbers = list(map(str, numbers))
print(string_numbers) # ['1', '2', '3', '4', '5']

squares = list(map(lambda x: x**2, numbers))
print(squares) # [1, 4, 9, 16, 25]

list1 = [1, 2, 3]
list2 = [4, 5, 6]
result = list(map(lambda x, y: x + y, list1, list2))
print(result) # [5, 7, 9]
```
`filter` - фильтрует элементы итерируемого объекта, оставляя только те, для которых функция возвращает True:
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
even_numbers = list(filter(lambda x: x % 2 == 0, numbers))
print(even_numbers)  # [2, 4, 6, 8, 10]

# Комбинирование map и filter
even_squares = list(map(lambda x: x**2, filter(lambda x: x % 2 == 0, numbers)))  # map
print(even_squares)  # [4, 16, 36, 64, 100]

even_squares_v2 = [x**2 for x in numbers if x % 2 == 0]  # list comprehension
print(even_squares_v2)  # [4, 16, 36, 64, 100]
```
`sorted` - сортировка итерируемого объекта:
```python
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
sorted_numbers = sorted(numbers)
print(sorted_numbers)  # [1, 1, 2, 3, 4, 5, 6, 9]

fruits = ['apple', 'banana', 'Cherry', 'date']
sorted_fruits = sorted(fruits)
print(sorted_fruits)  # ['Cherry', 'apple', 'banana', 'date'] (учитывает регистр)

fruits = ['apple', 'banana', 'cherry', 'date']
sorted_by_length = sorted(fruits, key=len)
print(sorted_by_length)  # ['date', 'apple', 'banana', 'cherry']
```
`reduce` - применяет функцию последовательно к элементам, сводя их к одному значению:
```python
from functools import reduce
numbers = [1, 2, 3, 4, 5]

# Сумма элементов
total = reduce(lambda x, y: x + y, numbers)
print(total)  # 15

# Произведение элементов
product = reduce(lambda x, y: x * y, numbers)
print(product)  # 120

# Поиск максимального элемента
maximum = reduce(lambda x, y: x if x > y else y, numbers)
print(maximum)  # 5
```