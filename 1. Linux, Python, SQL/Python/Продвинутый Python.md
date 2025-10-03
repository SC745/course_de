<h2>Продвинутый Python</h2>
<h4>1. Объектно-ориентированное программирование (ООП)</h4>

Объектно-ориентированное программирование (ООП) в Python основано на использовании классов и объектов. Это позволяет создавать код, который повторно используется и легко модифицируется. ООП в Python поддерживает концепции, такие как наследование, инкапсуляция, полиморфизм и абстракция.
<h5>Классы и объекты</h5>

Класс в Python — это шаблон для создания объектов, которые представляют сущности или концепции с определенными атрибутами (данными) и методами (функциями).
```python
class MyClass:
    x = 5
p1 = MyClass()  # Создание объекта p1 класса MyClass
print(p1.x)     # 5
```
<h5>Методы</h5>
Методы в классе — это функции, которые выполняют действия с объектами или изменяют их состояние. Метод `__init__` — это конструктор класса, который вызывается при создании нового объекта класса.

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    def greet(self):
        print(f"Hello, my name is {self.name} and I am {self.age} years old.")

maxim = Person("Максим", 36)
maxim.greet()
```
<h5>Наследование</h5>
Наследование позволяет одному классу наследовать атрибуты и методы другого. Это обеспечивает возможность повторного использования кода.

```python
class Student(Person):  # Student наследует от Person
    def __init__(self, name, age, year):
        super().__init__(name, age)
        self.graduationYear = year

    def welcome(self):
        print(f"Welcome, {self.name}, to the class of {self.graduationYear}.")

s1 = Student("Вероника", 21, 2024)
s1.welcome()
```
<h5>Инкапсуляция</h5>
Инкапсуляция — это ограничение доступа к данным класса, скрывая детали реализации от пользователя. В Python это достигается с помощью приватных атрибутов и методов, которые обозначаются двойным подчеркиванием __.

```python
class Computer:
    def __init__(self):
        self.__maxprice = 900
    def sell(self):
        print(f"Цена продажи: {self.__maxprice}")
    def setMaxPrice(self, price):
        self.__maxprice = price

c = Computer()
c.sell()
c.__maxprice = 1000  # Не изменит цену, так как __maxprice является приватным
c.sell()
c.setMaxPrice(1000)
c.sell()
```

<h5>Полиморфизм</h5>
Полиморфизм позволяет объектам разных классов иметь методы с одинаковым именем, которые могут быть вызваны из одного интерфейса.

```python
class Fruit:
    def __init__(self, cultivar):
        self.__cultivar = cultivar
    def type(self):
        print("Фрукт")

class Apple(Fruit):
    def type(self):
        print("Яблоко")

class Peach(Fruit):
    def type(self):
        print("Персик")

# Инициализация объектов
fruit = Fruit("-")
apple = Apple("Антоновка")
peach = Peach("Кардинал")

# Полиморфизм переопределением
for x in (fruit, apple, peach):
	x.type()
	print(f"Сорт: {x._Fruit__cultivar}")

# Полиморфизм через интерфейсную функцию
def func(obj):
    obj.type()
func(apple)    # Яблоко
func(peach)    # Персик
```

<h5>Абстракция</h5>
Абстракция позволяет скрыть сложные детали реализации и показать только необходимые аспекты объекта пользователю. Также они позволяют явно указать, какие методы должны быть реализованы в классах-потомках.
В Python абстрактные классы могут быть созданы с помощью модуля abc и добавления аннотации `@abstractmethod`:

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.__width = width
        self.__height = height

    def area(self):
        return self.__width * self.__height

rect = Rectangle(10, 20)
print(rect.area())
```

<h5>Множественное наследование</h5>

```python
class A:
    def method(self):
        return "A"

class B(A):
    def method(self):
        return "B"

class C(A):
    def method(self):
        return "C"

class D(B, C):
    pass

# Проверка MRO
print(D.__mro__)
# (<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>)

d = D()
print(d.method())  # B (ищет в порядке D -> B -> C -> A)
```
<h4>2. Работа с файлами</h4>

Использование контекстного менеджера для открытия файлов:
```python
with open('example.txt', 'r') as file:
    content = file.read()
    # Файл автоматически закроется при выходе из блока

with open('example.txt', 'w') as file:
    file.write('Hello, World!')
```

Python поддерживает следующие режимы открытия файлов:
- `r`  - чтение (по умолчанию)
- `w`  - запись (перезаписывает файл)
- `a`  - добавление в конец файла
- `x`  - эксклюзивное создание (ошибка если файл существует)
- `b`  - бинарный режим
- `t`  - текстовый режим (по умолчанию)
- `+`  - чтение и запись

Примеры основных комбинаций режимов:
```python
with open('file.txt', 'r') as f:    # Чтение текстового файла
    pass
with open('file.bin', 'wb') as f:   # Запись бинарного файла
    pass
with open('file.txt', 'a') as f:    # Добавление в текстовый файл
    pass
with open('file.txt', 'r+') as f:   # Чтение и запись
    pass
with open('file.txt', 'w+') as f:   # Чтение и запись (перезаписывает)
    pass
```

<h5>Чтение</h5>

```python
# read() - чтение всего файла
with open('example.txt', 'r') as file:
    content = file.read()
    print("Весь файл:")
    print(content)

# readline() - чтение одной строки
with open('example.txt', 'r') as file:
    first_line = file.readline()
    second_line = file.readline()
    print("Первая строка:", first_line)
    print("Вторая строка:", second_line)

# readlines() - чтение всех строк в список
with open('example.txt', 'r') as file:
    lines = file.readlines()
    print("Все строки:")
    for line in lines:
        print(line.strip())  # strip() убирает символы перевода строки
```

<h5>Запись</h5>

```python
# write() - запись строки
with open('output.txt', 'w') as file:
    file.write('Первая строка\n')
    file.write('Вторая строка\n')

# writelines() - запись списка строк
lines = ['Первая строка\n', 'Вторая строка\n', 'Третья строка\n']
with open('output.txt', 'w') as file:
    file.writelines(lines)
```

<h5>Сценарии</h5>

Копирование файла:
```python
def copy_binary_file(source, destination):
    with open(source, 'rb') as src_file:
        with open(destination, 'wb') as dst_file:
            # Читаем и записываем блоками для эффективности
            while True:
                chunk = src_file.read(4096)  # 4KB блоки
                if not chunk:
                    break
                dst_file.write(chunk)
```

Использование генератора для чтения больших файлов блоками:
```python
def read_in_chunks(file_path, chunk_size=1024):
    with open(file_path, 'r') as file:
        while True:
            chunk = file.read(chunk_size)
            if not chunk:
                break
            yield chunk

for chunk in read_in_chunks('very_large_file.txt'):
    process_chunk(chunk)  # Ваша функция обработки
```

<h4>3. Контекстные менеджеры</h4>

Контекстные менеджеры — это объекты, которые определяют контекст выполнения блока кода. Они обеспечивают выполнение кода при входе в контекст и при выходе из него, что позволяет управлять ресурсами (например, файлами, сетевыми соединениями, транзакциями) и гарантировать, что они будут корректно закрыты или освобождены, даже если в блоке кода произошла ошибка.
Блок `with` имеет следующий синтаксис:
```python
with выражение_как_менеджер_контекста as переменная:
    блок_кода
```
Выражение, используемое в with, должно возвращать объект контекстного менеджера. Такой объект должен иметь два метода: `__enter__()` и `__exit__()`.
- Метод `__enter__()` выполняется при входе в блок with. Его возвращаемое значение присваивается переменной после as (если она указана).
- Метод `__exit__()` выполняется при выходе из блока with (нормальном или из-за исключения).

Контекстный менеджер для измерения времени выполнения:
```python
import time

class Timer:
    def __enter__(self):
        self.start = time.time()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.end = time.time()
        self.interval = self.end - self.start
        print(f"Блок выполнялся {self.interval:.2f} секунд")

# Использование
with Timer():
    time.sleep(1)
```
Метод `__exit__` получает три аргумента, связанные с исключением (если оно произошло):
- `exc_type`: тип исключения.
- `exc_val`: экземпляр исключения.
- `exc_tb`: трассировка стека.

Если блок завершился без исключения, все эти аргументы будут None. Если метод `__exit__` возвращает True, это означает, что исключение было обработано и не должно быть проброшено дальше. Если возвращается False (или ничего, так как по умолчанию возвращается None), исключение будет проброшено после завершения метода `__exit__`.

<h5>Использование contextlib</h5>

Модуль contextlib предоставляет утилиты для создания контекстных менеджеров. Декоратор `@contextmanager` позволяет создать контекстный менеджер с помощью генераторной функции. До yield выполняется код `__enter__`, после `yield` — код `__exit__`.

from contextlib import contextmanager

```python
@contextmanager
def timer():
    start = time.time()
    try:
        yield
    finally:
        end = time.time()
        print(f"Блок выполнялся {end - start:.2f} секунд")

with timer():
    time.sleep(1)
```
Если в блоке `with` возникает исключение, оно будет проброшено в точку `yield`, поэтому оборачиваем `yield` в `try...finally`, чтобы гарантировать выполнение кода после `yield`.

<h4>4. Декораторы</h4>

Декораторы — это функции, которые модифицируют поведение других функций или методов. Они позволяют добавлять новую функциональность к существующим функциям без изменения их кода.

<h5>Базовый декоратор</h5>

```python
def simple_decorator(func):
    def wrapper():
        print("Действие до функции")
        func()
        print("Действие после функции")
    return wrapper

@simple_decorator
def say_hello():
    print("Привет!")
```
<h5>Декоратор для функции с аргументами</h5>

```python
def logger(func):
    def wrapper(*args, **kwargs):
        print(f"Вызов функции {func.__name__} с аргументами: {args}, {kwargs}")
        result = func(*args, **kwargs)
        print(f"Функция {func.__name__} вернула: {result}")
        return result
    return wrapper

@logger
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

result2 = greet("Анна", greeting="Привет")
# Вызов функции greet с аргументами: ('Анна',), {'greeting': 'Привет'}
# Функция greet вернула: Привет, Анна!
```

<h5>Декоратор с параметрами</h5>

```python
def repeat(times):
    """Декоратор, который повторяет выполнение функции заданное количество раз"""
    def decorator(func):
        def wrapper(*args, **kwargs):
            results = []
            for i in range(times):
                print(f"Повтор {i + 1}/{times}")
                result = func(*args, **kwargs)
                results.append(result)
            return results
        return wrapper
    return decorator

@repeat(times=3)
def say_hello(name):
    return f"Привет, {name}!"

# Использование
results = say_hello("Мир")
# Повтор 1/3
# Повтор 2/3
# Повтор 3/3
print(results)  # ['Привет, Мир!', 'Привет, Мир!', 'Привет, Мир!']

# Эквивалентно: say_hello = repeat(times=3)(say_hello)
```

<h4>5. Итераторы и генераторы</h4>

<h5>Итераторы</h5>

Итераторы — это объекты, которые позволяют перебирать элементы коллекции по одному за раз. В Python итератор реализует два метода:
- `__iter__()`: возвращает сам объект итератора.

- `__next__()`: возвращает следующий элемент. Если элементов больше нет, то выбрасывает исключение `StopIteration`.

Любой объект, который является итератором, можно использовать в цикле `for` или с функцией `next()`.
Пример (возвращение чисел от 0 до n):

```python
class MyIterator:
    def __init__(self, limit):
        self.limit = limit
        self.current = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < self.limit:
            value = self.current
            self.current += 1
            return value
        else:
            raise StopIteration

# Использование
my_iter = MyIterator(5)
for num in my_iter:
    print(num)
```

Важно различать:

- Итерируемый объект (`Iterable`): объект, который может возвращать итератор (имеет метод `__iter__()`). Например, списки, кортежи, строки.
- Итератор (`Iterator`): объект, который возвращает элементы по одному (имеет метод `__next__()`).

Цикл `for` автоматически создает итератор из итерируемого объекта.

<h5>Генераторы</h5>

Генераторы — это более удобный способ создания итераторов. Генераторы создаются с помощью функций с `yield` и генераторных выражений.
Пример генератора, который ведет себя так же, как `MyIterator`:
```python
limit = 5
def my_generator(limit):
    current = 0
    while current < limit:
        yield current
        current += 1

# Использование
for num in my_generator(limit):
    print(num)
```
Аналогично с помощью генераторного выражения:
```python
limit = 5
my_generator = (i for i in range(limit))

# Использование
for num in my_generator:
    print(num)
```
<h4>5. Параллельное программирование</h4>

Поток — это наименьшая единица выполнения в процессе. Многопоточность позволяет программе выполнять несколько задач параллельно в рамках одного процесса. Модуль `threading` предоставляет высокоуровневый интерфейс для работы с потоками в Python. Создадим 2 потока, которые будут выводить числа в диапазоне параллельно с заданной периодичностью:

```python
import threading
import time

range1 = 30
range2 = 40

interval1 = 0.5
interval2 = 0.2

def count_numbers(thread_name, count_range, count_interval):
    for i in range(count_range):
        print(f"{thread_name}: {i}")
        time.sleep(count_interval)
    print(f"{thread_name} завершен")

# Создание потоков
t1 = threading.Thread(target=count_numbers, args=("Counter-A", range1, interval1))
t2 = threading.Thread(target=count_numbers, args=("Counter-B", range2, interval2))

t1.start()
t2.start()
```
Также можно создавать потоки с помощью наследования от threading.Thread:
```python
import threading
import time

range1 = 30
range2 = 40

interval1 = 0.5
interval2 = 0.2

class MyThread(threading.Thread):
    def __init__(self, thread_id, thread_name, count_range, count_interval):
        threading.Thread.__init__(self)
        self.thread_id = thread_id
        self.thread_name = thread_name
        self.count_range = count_range
        self.count_interval = count_interval

    def run(self):
        self.count_numbers()
        print(f"{self.thread_name} завершен")

    def count_numbers(self):
        for i in range(self.count_range):
            print(f"{self.thread_name}: {i}")
            time.sleep(self.count_interval)

# Создание потоков
t1 = MyThread(1, "Counter-A", range1, interval1)
t2 = MyThread(2, "Counter-B", range2, interval2)

t1.start()
t2.start()
```
