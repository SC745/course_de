<h2>Базовый SQL</h2>
<h3>1. Использование переменных</h3>

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

<h3>1. MERGE, REPLACE, UPSERT</h3>

<h4>MERGE</h4>

`MERGE` позволяет выполнять операции `INSERT`, `UPDATE` и `DELETE` в одной команде на основе условий совпадения между целевой таблицей и источником данных. Она позволяет синхронизировать данные между двумя таблицами на основе условия совпадения.

Синтаксис:
```sql
MERGE INTO target_table [AS target_alias]
USING source_table [AS source_alias]
ON (merge_condition)
WHEN MATCHED [AND condition] THEN
    UPDATE SET column1 = value1, column2 = value2, ...
WHEN NOT MATCHED [BY TARGET] [AND condition] THEN
    INSERT (column1, column2, ...) VALUES (value1, value2, ...)
WHEN NOT MATCHED BY SOURCE [AND condition] THEN
    DELETE;
```
- `INTO` - целевая таблица для изменений
- `USING` - источник данных (таблица, представление, подзапрос)
- `ON` - условие соединения между целевой таблицей и источником
- `WHEN MATCHED` - действие при совпадении строк
- `WHEN NOT MATCHED BY TARGET` - действие когда строка есть в источнике, но нет в цели
- `WHEN NOT MATCHED BY SOURCE` - действие когда строка есть в цели, но нет в источнике

Пример синхронизации таблиц (вставка новых записей, обновление существующих, удаление старых):
```sql
MERGE INTO Products AS target
USING Updates AS source
ON target.ID = source.ID
WHEN MATCHED THEN
    UPDATE SET Price = source.Price
WHEN NOT MATCHED BY TARGET THEN
    INSERT (ID, Name, Price) VALUES (source.ID, source.Name, source.Price)
WHEN NOT MATCHED BY SOURCE
    THEN DELETE;
```

Для каждой операции (`INSERT`, `UPDATE`, `DELETE`) в `MERGE` срабатывают соответствующие триггеры. В SQL Server триггеры активируются для каждой строки, а не для всего оператора `MERGE`. То есть если MERGE обновляет 3 строки, триггер `UPDATE` сработает 3 раза, а если выполняется `INSERT` и `UPDATE` для разных строк, сработают оба типа триггеров.

Особенности в различных СУБД:
1. SQL Server:
  - Обязательно завершать оператор точкой с запятой.
  - Можно использовать таблицы, представления или табличные выражения в USING.
  - Если в источнике есть дубликаты по условию ON, операция завершится ошибкой.
2. Oracle:
  - Не требуется точка с запятой в конце оператора (если не в блоке PL/SQL)
  - Условие `ON` обязательно в круглых скобках
  - Ключевое слово `THEN` обязательно после каждого условия
  - Нельзя обновлять столбцы, участвующие в условии `ON`
  - `DELETE` может использоваться только после `UPDATE` в `WHEN MATCHED`
  - Можно использовать подзапросы в `USING`
  - Можно использовать условия в `WHEN`
  - Обязателен индекс по столбцам условия `ON`
  - Для каждой строки срабатывает только один тип операции (`INSERT`/`UPDATE`/`DELETE`)
  - Триггеры срабатывают в порядке: `BEFORE STATEMENT` → `BEFORE ROW` → `AFTER ROW` → `AFTER STATEMENT`

Синтаксис в Oracle:
```sql
MERGE INTO target_table target
USING source_table source
ON (target.id = source.id)
WHEN MATCHED THEN
    UPDATE SET column1 = source.value1
    [DELETE WHERE condition]
WHEN NOT MATCHED THEN
    INSERT (column1, ...) VALUES (source.value1, ...);
```

<h4>REPLACE</h4>

`REPLACE` - это команда MySQL, которая работает как `INSERT`, но автоматически удаляет и заменяет существующие строки при конфликте уникальных ключей. В отличие от `INSERT` работает медленнее, так как выполняет `DELETE` и `INSERT`, также автоинкрементные столбцы будут увеличиваться.

Синтаксис:
```sql
-- Стандартный синтаксис
REPLACE INTO table_name (column1, column2, ...) 
VALUES (value1, value2, ...);

-- С множественными строками
REPLACE INTO table_name (column1, column2, ...) 
VALUES 
    (value1, value2, ...),
    (value3, value4, ...);

-- Из другого запроса
REPLACE INTO table_name (column1, column2, ...)
SELECT column1, column2, ... FROM other_table;
```

При использовании `REPLACE` индексы перестраиваются после каждой операции, что может негативно сказаться на производительности, поэтому для частых обновлений лучше использовать `UPDATE`. В транзакционных движках (InnoDB) при использовании внутри транзакции `REPLACE` можно откатить:
```sql
START TRANSACTION;
REPLACE INTO accounts (account_id, balance) VALUES (101, 1000.00);
REPLACE INTO transactions (txn_id, account_id, amount) VALUES (1, 101, 100.00);
ROLLBACK;
-- Оба REPLACE откатываются
```

<h4>UPSERT</h4>

`UPSERT` — это совмещение двух команд: `UPDATE` и `INSERT`. Это операция, которая обновляет существующую запись, если она уже есть в таблице, или вставляет новую, если такой записи нет. Для работы UPSERT необходимо наличие уникального индекса или первичного ключа. Именно по этому индексу СУБД определяет, существует ли уже такая запись.

Пример - реализация `UPSERT` в MySQL через `ON DUPLICATE KEY UPDATE`:
```sql
INSERT INTO users (id, email, name)
VALUES (1, 'ivan@example.com', 'Ivan')
ON DUPLICATE KEY UPDATE
    name = VALUES(name),
    login_count = login_count + 1;
```
Если пользователя с `email = 'ivan@example.com'` (если на этом поле есть уникальный ключ) или `id = 1` нет, будет выполнена обычная вставка (`INSERT`). Если пользователь с таким `email` есть, то вместо вставки выполнится обновление (`UPDATE`): поле name будет установлено в `'Ivan'`, а `login_count` будет увеличено на 1.

В различных СУБД `UPSERT` может быть рализована по разному:
- MySQL - проверяется нарушение любых уникальных ключей: `INSERT ... ON DUPLICATE KEY UPDATE`
- PostgreSQL, SQLite - позволяет указать, по какому именно ограничению (CONSTRAINT) проверять конфликт: `INSERT ... ON CONFLICT ... DO UPDATE`
- Oracle, SQL Server - используют `MERGE`

Пример - реализация `UPSERT` в PostgreSQL через `ON CONFLICT`:
```sql
INSERT INTO users (email, name, login_count, last_login)
VALUES ('ivan@example.com', 'Ivan Ivanov', 1, NOW())
ON CONFLICT (email)
DO UPDATE SET
    name = EXCLUDED.name,
    last_login = EXCLUDED.last_login,
    login_count = users.login_count + 1
WHERE users.last_login < EXCLUDED.last_login;
-- Обновление произойдет только если новое время логина более свежее
```
`EXCLUDED` - это аналог `VALUES` из MySQL
Также можно предотвратить изменения с помощью инструкции `DO NOTHING`

Ключевые преимущества PostgreSQL UPSERT:
- Явное указание конфликта - можно точно указать, по какому полю/ограничению проверять конфликт
- `DO UPDATE` с условиями `WHERE`
- `DO NOTHING` когда обновление не требуется
- `RETURNING` для получения информации о результате операции