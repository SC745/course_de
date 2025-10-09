<h2>Базовый SQL</h2>
<h3>7. Использование переменных</h3>

Переменные в SQL могут хранить значение или набор значений (результат подзапроса). Они объявляются следующим образом (MySQL):
```sql
SET @var_name = значение;
SELECT @var_name := значение;
```

Пример использования в запросе:
```sql
SELECT @max_id := MAX(id) FROM users;
SELECT * FROM users WHERE id = @max_id;
```

Также переменные можно передавать из внешних систем. Рассмотрим пример для PostgreSQL с использованием библиотеки psycopg2 в Python (передаем кортежи с переменными):
```python
import psycopg2

conn = psycopg2.connect("dbname=test user=postgres")
cursor = conn.cursor()

# Пример с одной переменной
user_id = 123
cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))

# Пример с несколькими переменными
name = "Alice"
age = 30
cursor.execute("INSERT INTO users (name, age) VALUES (%s, %s)", (name, age))

# Именованные переменные
data = {"name": "Alice", "age": 30}
cursor.execute(
    "INSERT INTO users (name, age) VALUES (%(name)s, %(age)s)",
    data
)

conn.commit()
```

Передача массивов:
```python
import psycopg2

conn = psycopg2.connect("dbname=test user=postgres")
cursor = conn.cursor()

ids = [1, 2, 3]

# Способ 1: Через кортеж (для небольшого числа элементов)
cursor.execute("SELECT * FROM users WHERE id IN %s", (tuple(ids),))

# Способ 2: Через ANY (лучше для больших массивов)
cursor.execute("SELECT * FROM users WHERE id = ANY(%s)", (ids,))
```
