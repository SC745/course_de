<h2>Руководство по Pandas</h2>
<h3>1. Введение</h3>

`Pandas` — это библиотека Python, предоставляющая высокоуровневые структуры данных и широкий инструментарий для анализа данных. Основными структурами данных в `Pandas` являются `DataFrame` и `Series`.

- `Series` — это одномерный массив данных, который может содержать любой тип данных (целые числа, строки, вещественные числа, Python-объекты и т. д.). Он похож на колонку в таблице данных. Каждому элементу в `Series` соответствует индекс, который играет роль ключа в словаре. Индекс по умолчанию состоит из целых чисел от 0 до N-1, где N — это количество элементов в `Series`. Однако индекс может быть также установлен вручную, что позволяет использовать более значимые метки.
- `DataFrame` представляет собой двумерную, изменяемую таблицу данных с маркированными осями (строки и столбцы). Каждый столбец в `DataFrame` является объектом `Series`.

Многие методы `DataFrame` и `Series` не изменяют исходный объект, а создают копию. Чтобы этого избежать нужно использовать параметр `inplace`:
```python
inplace = true
```

<h4>Создание, импорт и экспорт данных</h4>

```python
import pandas as pd
import numpy as np

# Из словаря
df = pd.DataFrame({
    'name': ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve', 'Frank'],
    'age': [25, 30, 35, 28, 32, 40],
    'city': ['New York', 'London', 'Paris', 'Tokyo', 'Berlin', 'London'],
    'salary': [50000, 60000, 70000, 55000, 65000, 80000],
    'department': ['IT', 'HR', 'IT', 'Finance', 'IT', 'HR'],
    'experience': [2, 5, 8, 3, 6, 12]
})

# Из списка списков
df = pd.DataFrame([[1, 'a'], [2, 'b']], columns=['col1', 'col2'])

# Из NumPy массива
df = pd.DataFrame(np.random.randn(3, 2))

# Чтение файлов
df = pd.read_csv('file.csv')
df = pd.read_excel('file.xlsx')
df = pd.read_json('file.json')
```

<h4>Просмотр и основная информация</h4>

```python
df.head()           # Первые 5 строк
df.tail(3)          # Последние 3 строки
df.sample(5)        # Случайные 5 строк
df.shape            # Размерность (строки, столбцы)
df.columns          # Названия столбцов
df.index            # Индексы
df.info()           # Общая информация
df.describe()       # Статистика по числовым столбцам
df.dtypes           # Типы данных каждого столбца
df.nunique()        # Уникальные значения в каждом столбце
```

<h3>2. Выборка данных</h3>
<h4>Выбор столбцов и строк</h4>

```python
# Выбор столбцов
df['col_name']          # Один столбец (Series)
df[['col1', 'col2']]    # Несколько столбцов (DataFrame)
df.col_name             # Точечная нотация (только если имя без пробелов)

# Выбор строк
df.loc[]                # Выбор по меткам
df.iloc[]               # Выбор по позициям
df[df['age'] > 30]      # Булева индексация

# Комбинирование
df.loc[0:2, 'col1':'col3']                # Срезы по меткам
df.iloc[0:3, 1:4]                         # Срезы по позициям
df.loc[df['age'] > 30, ['col1','age']]    # Пример с loc
df[df['age'] > 30][['col1','age']]        # Аналогичный пример
```

<h4>Фильтрация данных</h4>

Простые условия:
```python
df[df['age'] > 30]            # Фильтрация по одному условию
df[df['city'] == 'London']    # Фильтрация по равенству
df[df['city'] != 'London']    # Фильтрация по неравенству
```

Комбинирование условий:
```python
df[(df['department'] == 'IT') & (df['age'] > 30)]        # AND условие (&)
df[(df['city'] == 'London') | (df['salary'] > 65000)]    # OR условие (|)
df[~((df['department'] == 'IT') & (df['age'] > 30))]     # NOT условие (~)
```

Методы для фильтрации:
```python
df[df['department'].isin(['IT', 'Finance'])]    # isin() - вхождение в список
df[df['age'].between(25, 35)]                   # between() - вхождение в интервал
df.where(df['salary'] > 6000, other = '123')    # where() - замена несоответствий
df.mask(df['salary'] > 6000, other = '123')     # mask() - замена соответствий
df.nlargest(3, 'salary')                        # nlargest() - топ самых больших
df.nsmallest(2, 'age')                          # nsmallest() - топ самых маленьких
```

<h4>Поиск пропущенных значений</h4>

```python
df.isnull().sum()                          # Количество пропусков по столбцам
df.isnull().sum().sum()                    # Общее количество пропусков
df[df.isnull().any(axis=1)]                # Строки с пропусками
df.columns[df.isnull().any()]              # Столбцы с пропусками
missing_names = df[df['name'].isnull()]    # Поиск конкретных пропусков
```

<h4>Метод query()</h4>

```python
# Простые сравнения
df.query('age > 30')              # Больше
df.query('salary >= 60000')       # Больше или равно
df.query('age < 40')              # Меньше
df.query('salary <= 70000')       # Меньше или равно
df.query('department == "IT"')    # Равно (строки в двойных кавычках)
df.query("city == 'London'")      # Равно (строки в одинарных кавычках)
df.query('department != "HR"')    # Не равно

# Логические операторы
df.query('age > 30 and salary > 60000')                          # AND - и
df.query('department == "IT" or department == "Finance"')        # OR - или
df.query('not (department == "HR")')                             # NOT - не
df.query('department in ["IT", "Finance"]')                      # IN - в
df.query('(age > 30 and salary > 60000) or city == "London"')    # Комбинирование

# Арифметические операции
df.query('salary / age > 2000')             # Деление
df.query('salary * 1.1 > 70000')            # Умножение
df.query('age + 5 > 40')                    # Сложение
df.query('salary - 10000 > 50000')          # Вычитание
df.query('(salary - 50000) / age > 500')    # Сложное выражение
df.query('salary > age * 2000')             # Сравнение с выражением

# Использование переменных
df.query('age >= @min_age and salary <= @max_salary')                        # Числовые переменные
df.query('department == @department and city == @city')                      # Строковые переменные
df.query('age in @valid_ages and department in @departments')                # Списки
df.query('salary >= @params["min_salary"] and age <= @params["max_age"]')    # Словари

# Применение методов
df.query('name.str.len() > 10')                       # Метод строки
df.query('date.dt.month == 1')                        # Метод даты
df.query('salary > 50000 + math.sqrt(age) * 1000')    # Функция math
```

<h3>3. Изменение данных</h3>
<h4>Работа с пропущеными значениями</h4>




<h3>4. Изменение структуры</h3>
<h4>Работа со столбцами</h4>

Добавление столбца:
```python
df['bonus'] = [0.1, 0.1, 0.3, 0.2]                             # Добавление столбца через присваивание
df['salary_after_bonus'] = df['salary'] * (1 + df['bonus'])    # Добавление вычисляемого столбца
df['company'] = 'TechCorp'                                     # Добавление столбца с константой
```

Переименование столбцов:
```python
df.rename(columns={'name': 'full_name', 'age': 'years_old'})    # Отдельные столбцы
df.columns = [col.upper() for col in df.columns]                # Все столбцы
```

Удаление столбцов:
```python
df.drop('company', axis=1)                          # Удаление одного столбца
df.drop(['bonus', 'salary_after_bonus'], axis=1)    # Удаление нескольких столбцов
df = df[['name', 'age', 'salary', 'department']]    # Удаление через выбор оставляемых столбцов
df.drop(df.columns[[5, 6]], axis=1)                 # Удаление по позициям
```

Изменение порядка столбцов:
```python
# Изменение порядка через список столбцов
df = df[['name', 'department', 'age', 'salary', 'experience', 'seniority', 'city', 'company']]

# Динамическое изменение порядка
cols = df.columns.tolist()
cols.insert(1, cols.pop(cols.index('department')))    # Перемещаем department после name
df = df[cols]

# Сортировка столбцов
df = df.sort_index(axis=1)    # По алфавиту
```

Изменение типа данных столбцов:
```python
# DataFrame с разными типами данных
mixed_df = pd.DataFrame({
    'integers_as_strings': ['1', '2', '3', '4'],
    'floats_as_strings': ['1.5', '2.5', '3.5', '4.5'],
    'dates_as_strings': ['2024-01-01', '2024-02-01', '2024-03-01', '2024-04-01'],
    'booleans_as_strings': ['True', 'False', 'True', 'False']
})

# Изменение типов данных
mixed_df['integers'] = mixed_df['integers_as_strings'].astype(int)
mixed_df['floats'] = mixed_df['floats_as_strings'].astype(float)
mixed_df['dates'] = pd.to_datetime(mixed_df['dates_as_strings'])
mixed_df['booleans'] = mixed_df['booleans_as_strings'].map({'True': True, 'False': False})

# Использование astype для нескольких столбцов
conversion_dict = {'integers_as_strings': 'int32', 'floats_as_strings': 'float64'}
df_converted = mixed_df.astype(conversion_dict)
```

<h4>Работа с индексами</h4>

Установка индекса:
```python
df.set_index('name')                    # Установка столбца как индекса
df.set_index(['department', 'name'])    # Установка MultiIndex
df.set_index('name', drop=False)        # Установка индекса с сохранением столбца
```

Сброс индекса:
```python
df.reset_index()                # Сброс индекса в столбец
df.reset_index(level='name')    # Сброс только определенных уровней MultiIndex
df.reset_index(drop=True)       # Сброс с удалением индекса
```

Изменение индекса:
```python
df.index = ['A', 'B', 'C', 'D']                                # Прямое изменение индекса
df.index = pd.RangeIndex(start=100, stop=104, step=1)          # Создание индекса из диапазона
df.index = pd.date_range('2024-01-01', periods=4, freq='D')    # Индекс из дат
```

Переименование индекса:
```python
df.rename_axis('employee_name')                          # Переименование индекса
df.rename_axis(['dept', 'emp_name'])                     # Переименование уровней MultiIndex
df.rename(index={'Alice': 'Alicia', 'Bob': 'Robert'})    # Переименование конкретных меток индекса
```

Сортировка индекса:
```python
df.sort_index()                      # Сортировка по индексу
df.sort_index(level='department')    # Сортировка MultiIndex
df.sort_index(ascending=False)       # Сортировка в обратном порядке
```