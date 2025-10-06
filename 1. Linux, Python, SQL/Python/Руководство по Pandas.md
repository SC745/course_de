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

Удаление пропущенных значений:
```python
df.dropna()                # Удаление строк с пропущенными значениями
df.dropna(axis=1)          # Удаление столбцов с пропущенными значениями
df.dropna(thresh=2)        # Оставляет строки с минимум 2 не-NaN значениями
df.dropna(subset=['A'])    # Удаляет строки, где в столбце 'A' есть NaN
```

Замена пропущенных значений:
```python
df.fillna(0)                      # Заполнение константой
df['A'].fillna(df['A'].mean())    # Заполнение средним/медианой
df.fillna(method='ffill')         # Заполнение предыдущим значением. Аналог: df.ffill()
df.fillna(method='bfill')         # Заполнение следующим значением. Аналог: df.ffill()
```

<h4>Замена значений</h4>

Метод `replace()` - замена значений в `Series` и `DataFrame`:
```python
df.replace('foo', 'FOO')                      # Замена одного значения
df.replace(['foo', 'bar'], ['FOO', 'BAR'])    # Замена нескольких значений
df.replace({'foo': 'bar', 'FOO': 'BAR'})      # Замена с помощью словаря
df['name'].replace({'FOO': 'BAR'})            # Замена в конкретном столбце
df['salary'].replace([df['salary'] > 80000, df['salary'] > 60000], ['High', 'Average'])    # Замена по условию
```

Метод `apply()` - применение функций к `Series` и `DataFrame`:
```python
df['age'].apply(lambda x: x * 2)    # Применение к столбцу
df.apply(sum, axis=0)               # Применение ко всем столбцам
df.apply(sum, axis=1)               # Применение ко всем строкам

# Пользовательская функция
def calculate_bonus(salary, bonus_rate=0.1, fixed_bonus=0):
    return salary * bonus_rate + fixed_bonus

# Применяем с дополнительными аргументами
df['salary'].apply(calculate_bonus, args=(0.15, 1000))
```

Метод `map()` - замена значений в `Series`:
```python
df['department'].map({'Finance': 'FN'})                        # Использование словаря
df['name'].map(str.upper)                                      # Использование функции
df['name'].map(pd.Series(['Finance'], index = ['FN']))         # Использование Series
df['department'].map({'Finance': 'FN'}, na_action='ignore')    # Обработка NaN
```

<h4>Группировка данных</h4>

Основные группировки:
```python
df.groupby('city')['sales'].sum()                                   # Сумма продаж по городам (Series)
df.groupby('city')['sales'].agg(['sum', 'mean', 'count', 'std'])    # Несколько статистик по городам (DataFrame)
df.groupby('city').agg({'sales': ['sum', 'mean'], 'count': ['sum', 'mean']})    # Агрегация по нескольким столбцам
df.groupby(['city', 'product']).agg({'sales': ['sum', 'mean'], 'count': 'sum'}) # Группировка по нескольким столбцам
df.groupby(['city', 'product']).loc[('Moscow', 'apple')]           # Получение данных для конкретной группы
df.groupby(['city', 'product']).get_group(('Москва', 'Яблоко'))    # Альтернативный способ

# Применение пользовательской функции
def sales_range(series): return series.max() - series.min()
df.groupby('Город')['Продажи'].agg([sales_range])
```

Трансформация данных в группах (аналог оконных функций):
```python
df.groupby('city')['sales'].transform('mean')                                # Среднее по группе в каждой ее строке
df.groupby('city')['sales'].transform(lambda x: (x - x.mean()) / x.std())    # Нормализация значений (z-score)
df.groupby('city')['sales'].transform(lambda x: x.fillna(x.mean()))          # Замена пропущенных на среднее в группе
df.groupby('department')['salary'].transform(lambda x: x.rank())             # Ранг по зарплате
df.groupby('city')['sales'].transform('cumsum')                              # Кумулятивная сумма по группам
df.groupby('city')['sales'].transform('cummax')                              # Кумулятивное максимальное значение
df.groupby('city')['sales'].transform(lambda x: x.rolling(window=3, min_periods=1).mean())    # Скользящее среднее
```

<h4>Работа с датой и временем</h4>

Базовые преобразования:
```python
data = {
    'date_str1': ['2024-01-01', '2024-01-02', '2024-01-03'],
    'date_str2': ['01/01/2024', '02/01/2024', '03/01/2024'],
    'date_str3': ['2024-01-01 10:30:00', '2024-01-02 14:45:00', '2024-01-03 18:15:00'],
    'timestamp': [1704067200, 1704153600, 1704240000]
}
df = pd.DataFrame(data)

pd.to_datetime(df['date_str1'])                       # Стандартное преобразование
pd.to_datetime(df['date_str2'], format='%d/%m/%Y')    # Преобразование через формат
pd.to_datetime(df['date_str3'])                       # Преобразование datetime строки
pd.to_datetime(df['timestamp'], unit='s')             # Преобразование Unix timestamp

pd.to_datetime(df['date_str1'], errors='coerce')      # invalid_date -> NaT
pd.to_datetime(df['date_str1'], errors='ignore')      # Оставляет как есть
```

Извлечение компонентов даты и времени:
```python
df['datetime'].dt.year
df['datetime'].dt.month
df['datetime'].dt.day
df['datetime'].dt.hour
df['datetime'].dt.minute
df['datetime'].dt.second
df['datetime'].dt.dayofweek  # 0-6 (понедельник=0)
df['datetime'].dt.day_name()
df['datetime'].dt.month_name()
df['datetime'].dt.quarter
df['datetime'].dt.dayofweek.isin([5, 6])
```

Преобразование в строки:
```python
format_codes = {
    '%Y': 'Год (4 цифры)',
    '%y': 'Год (2 цифры)',
    '%m': 'Месяц (число)',
    '%B': 'Полное название месяца',
    '%b': 'Сокращенное название месяца',
    '%d': 'День месяца',
    '%A': 'Полное название дня недели',
    '%a': 'Сокращенное название дня недели',
    '%H': 'Час (24-часовой формат)',
    '%I': 'Час (12-часовой формат)',
    '%p': 'AM/PM',
    '%M': 'Минуты',
    '%S': 'Секунды',
    '%f': 'Микросекунды'
}

df['datetime'].dt.strftime('%Y-%m-%d'),
df['datetime'].dt.strftime('%d.%m.%Y'),
df['datetime'].dt.strftime('%m/%d/%Y'),
df['datetime'].dt.strftime('%Y-%m-%d %H:%M:%S'),
df['datetime'].dt.strftime('%d %B %Y, %A'),
df['datetime'].dt.strftime('%Y-%m-%dT%H:%M:%S'),
df['datetime'].dt.strftime('Day: %d, Month: %m, Year: %Y')
```

Работа с временными зонами:
```python
# Локализация (предполагаем, что время в UTC)
df['naive_time'].dt.tz_localize('UTC')              # Установка временной зоны
df['utc_time'].dt.tz_convert('America/New_York')    # Конвертация временной зоны
df['utc_time'].dt.tz_convert('Europe/Moscow')       # Конвертация временной зоны
```

Арифметические операции с датами:
```python
# Добавление интервалов
df['start_date'] + pd.Timedelta(days=1)
df['start_date'] + pd.Timedelta(weeks=2)
df['start_date'] + pd.Timedelta(hours=3)

# Разница между датами
(df['end_date'] - df['start_date']).dt.days
(df['end_date'] - df['start_date']).dt.total_seconds() / 3600
```

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

<h3>5. Специальные возможности</h3>
<h4>Вычисление корреляции и ковариации</h4>

Ковариация - это статистическая мера, которая показывает направление линейной зависимости между двумя случайными величинами.
```
Cov(X,Y) = E[(X - E[X]) × (Y - E[Y])]
```
Корреляция - это статистическая мера, которая показывает направление и силу линейной зависимости между двумя случайными величинами.
```
ρ(X,Y) = Cov(X,Y) / (σ_X × σ_Y)
```
Вычисление в pandas:
```python
cov_matrix = df.cov()                      # Матрица ковариации для всего DataFrame
df['sales'].cov(df['marketing_spend'])     # Ковариация между двумя конкретными переменными

corr_matrix = df.corr()                    # Матрица корреляции (по Пирсону по умолчанию)
df['sales'].corr(df['marketing_spend'])    # Корреляция между двумя переменными
```

<h4>Работа с категориальными данными</h4>

Создание категориальных данных:
```python
df = pd.DataFrame({
    'product': ['A', 'B', 'C', 'A', 'B', 'C', 'A', 'B'],
    'sales': [100, 150, 200, 120, 180, 190, 110, 160],
    'region': ['North', 'South', 'North', 'South', 'East', 'West', 'East', 'West']
})

# Преобразование в категориальный тип
df['product'] = df['product'].astype('category')
df['region'] = df['region'].astype('category')
```

Создание упорядоченных категорий:
```python
size_order = ['Small', 'Medium', 'Large']
df['size'] = pd.Categorical(['Medium', 'Small', 'Large', 'Small', 'Medium'], categories=size_order, ordered=True)
df[df['size'] > 'Small']    # Сравнение по категории
```

Методы категориальных данных:
```python
df['region'].cat.add_categories(['Central'])                             # Добавление категории
df['region'].cat.remove_categories(['West'])                             # Удаление категории
df['region'].cat.remove_unused_categories()                              # Удаление неиспользуемых
df['region'].cat.rename_categories({'North': 'Север', 'South': 'Юг'})    # Переименование категорий
```

Разбиение на интервалы:
```python
df['age_group_cut'] = pd.cut(df['age'], bins=4)           # Разбиение на 4 равных интервала по значению
df['age_group_cut'].value_counts().sort_index()           # Количество вхождений в каждый интервал

df['age_group_qcut'] = pd.qcut(df['age'], q=4)            # Разбиение на 4 квантиля (равное количество наблюдений)
df['age_group_qcut'].value_counts().sort_index()          # Количество вхождений в каждый интервал

# Пользовательские интервалы
bins = [0, 25, 35, 45, 55, 65, 100]
labels = ['18-25', '26-35', '36-45', '46-55', '56-65', '66+']
df['age_custom'] = pd.cut(df['age'], bins=bins, labels=labels, right=False)
df['age_custom'].value_counts().sort_index()              # Количество вхождений в каждый интервал
```

<h4>Многомерная сводка данных с использованием pivot_table</h4>

```python
np.random.seed(42)
data = {
    'Region': ['North', 'North', 'South', 'South', 'East', 'East', 'West', 'West'] * 3,
    'Product': ['A', 'B', 'A', 'B', 'A', 'B', 'A', 'B'] * 3,
    'Quarter': ['Q1'] * 8 + ['Q2'] * 8 + ['Q3'] * 8,
    'Sales': np.random.randint(100, 500, 24),
    'Profit': np.random.randint(10, 100, 24),
    'Units': np.random.randint(1, 20, 24)
}

df = pd.DataFrame(data)

# Сумма продаж по регионам
pt1 = pd.pivot_table(df, values='Sales', index='Region', aggfunc='sum')

# Средние продажи по регионам и продуктам
pt2 = pd.pivot_table(df, values='Sales', index='Region', columns='Product', aggfunc='mean')

# Несколько метрик по нескольким измерениям
pt3 = pd.pivot_table(df,
                    values=['Sales', 'Profit', 'Units'],
                    index=['Region', 'Product'],
                    columns='Quarter',
                    aggfunc={'Sales': 'sum', 'Profit': 'mean', 'Units': 'sum'})
```