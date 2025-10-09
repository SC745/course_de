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

<h3>2. MERGE, REPLACE, UPSERT</h3>

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

<h3>3. Табличные выражения</h3>

CTE (Common Table Expression) - это временный результат запроса, который можно использовать в пределах выполнения одного SQL-запроса (`SELECT`, `INSERT`, `UPDATE`, `DELETE`). CTE определяется с помощью ключевого слова `WITH`:
```sql
WITH Sales_CTE (SalesPerson, SalesAmount) AS (
    SELECT SalesPerson, SUM(SalesAmount)
    FROM Sales
    GROUP BY SalesPerson
)
SELECT * FROM Sales_CTE WHERE SalesAmount > 1000;
```

<h4>Продвинутое использование CTE</h4>

Можно определить несколько CTE, объявляя их через запятую после `WITH`. Эти CTE могут взаимодействовать друг с другом в блоке `WITH` как обычные таблицы, главное определить их в правильном порядке. C CTE можно выполнять все те же действия в команде `SELECT`, что и с обычными таблицами. Например, поэтапная фильтрация:
```sql
WITH Customers_CTE AS (
    SELECT CustomerID, Country
    FROM Customers
    WHERE Country = 'USA'
),
Orders_CTE AS (
    SELECT OrderID, CustomerID, OrderDate
    FROM Orders
    WHERE CustomerID IN (SELECT CustomerID FROM Customers_CTE)
)
-- Итоговый запрос
SELECT * FROM Orders_CTE WHERE OrderDate >= '2023-01-01';
```

Также в CTE удобно применять оконные функции, а затем фильтровать в основном запросе (или в другом CTE):
```sql
WITH cte AS (
    SELECT orderid,
           orderdate,
           val,
           RANK() OVER(ORDER BY val DESC) AS rnk
      FROM Orders
)
SELECT * FROM cte WHERE rnk <= 5
```

<h4>Рекурсивные CTE</h4>

CTE также могут быть рекурсивными и использоваться для обхода иерархических структур. Рекурсивные CTE определяются с помощью ключевого слова `RECURSIVE` и их запрос состоит из двух частей:
- Якорная часть (Anchor): начальный набор данных.
- Рекурсивная часть (Recursive Member): часть, которая ссылается на саму CTE, чтобы добавить следующие уровни.

Пример (определение уровня глубины иерархии в компании для каждого сотрудника):
```sql
WITH RECURSIVE EmployeeHierarchy AS (
    SELECT EmployeeID, ManagerID, EmployeeName, 0 AS Level            -- Якорь
      FROM Employees
     WHERE ManagerID IS NULL
    UNION ALL
    SELECT e.EmployeeID, e.ManagerID, e.EmployeeName, eh.Level + 1    -- Рекурсия
      FROM Employees AS e
           JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT * FROM EmployeeHierarchy;
```

<h3>4. Оконные функции</h3>

Оконные функции в SQL позволяют выполнять вычисления в рамках окон (наборов строк, выбранных по определенному принципу), которые каким-то образом связаны с текущей строкой. В отличие от агрегатных функций, которые группируют строки и возвращают один результат на группу, оконные функции не приводят к свертыванию строк в одну. Они сохраняют все строки и добавляют новое вычисленное значение для каждой строки. Также они выполняются в последнюю очередь, а по результату оконной функции нельзя фильтровать в этом же запросе.

Виды оконных функций:
- Агрегатные (`SUM`, `AVG`, `COUNT`, `MIN`, `MAX`) в оконном контексте.
- Ранжирующие (`ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`).
- Функции смещения (`LAG`, `LEAD`, `FIRST_VALUE`, `LAST_VALUE`).

Пример (вывод средней зарплаты по всей компании в отдельную колонку):
```sql
SELECT name, salary, AVG(salary) OVER() as avg_salary
  FROM employees;
```

<h4>Ключевое слово OVER</h4>

Ключевое слово `OVER()` является обязательным для оконных функций. Оно определяет, как набор строк разбивается и упорядочивается для оконной функции. Полный синтаксис:
```sql
OVER (
    [PARTITION BY выражение1, выражение2, ...]
    [ORDER BY выражение3 [ASC|DESC] [NULLS FIRST|NULLS LAST], ...]
    [ROWS | RANGE | GROUPS BETWEEN граница_начала AND граница_окончания]
)
```

Элементы:
1. `PARTITION BY` - разделяет строки на группы (окна).
2. `ORDER BY` - определяет порядок данных внутри окна. Опции:
  - `ASC` (по возрастанию, по умолчанию) / `DESC` (по убыванию).
  - `NULLS FIRST` (`NULL` в начале) / `NULLS LAST` (`NULL` в конце).
3. Фрейм (область вычислений) - задаёт диапазон строк для расчёта относительно текущей строки:
  - Типы фреймов:
    - `ROWS `— физические строки.
    - `RANGE` — логические интервалы (учитывает повторяющиеся значения).
    - `GROUPS` — группы строк с одинаковыми значениями ORDER BY (поддержка зависит от СУБД).
  - Границы фрейма:
    - `UNBOUNDED PRECEDING` — начало окна.
    - `N PRECEDING` — N строк до текущей.
    - `CURRENT ROW` — текущая строка.
    - `N FOLLOWING` — N строк после текущей.
    - `UNBOUNDED FOLLOWING` — конец окна.

<h4>Ранжирующие функции</h4>

- `ROW_NUMBER()` - присваивает уникальный номер каждой строке в партиции, начиная с 1. Если в `ORDER BY` есть дубликаты, то они получат разные номера (порядок определяется неоднозначно, но номера уникальны). Например: 1, 2, 3, 4.
- `RANK()`: присваивает ранг каждой строке. Если есть дубликаты, они получат одинаковый ранг, а следующий ранг будет пропущен. Например: 1, 2, 2, 4.
- `DENSE_RANK()`: также присваивает одинаковый ранг дубликатам, но следующий ранг не пропускается. Например: 1, 2, 2, 3.
- `NTILE()`: разбивает данные на квантили (группы с равным количеством строк)

Пример (ранжирование сотрудников по зарплате по каждому отделу и разбиение на 3 группы по доходу по всей компании):
```sql
SELECT department, name, salary,
       DENSE_RANK() OVER(PARTITION BY department ORDER BY salary DESC) AS dep_rnk,
       NTILE(3) OVER(ORDER BY salary DESC) AS group,
  FROM employees;
```

<h4>Функции смещения</h4>

- `LAG()` — получает значение из предыдущей строки
- `LEAD()` — получает значение из следующей строки
- `FIRST_VALUE()` — получает первое значение в партиции
- `LAST_VALUE()` — получает последнее значение в партиции

В функциях LAG() и LEAD() можно задать отступ (по умолчанию 1) и значение, которое стоит установить, если функция выйдет за пределы партиции:
```sql
LAG | LEAD(выражение, отступ, значение_по_умолчанию)
```

Пример (анализ продаж по дням):
```sql
SELECT sale_date,
       amount,
       LAG(amount) OVER (ORDER BY sale_date) AS previous_day_sales,
       LEAD(amount) OVER (ORDER BY sale_date) AS next_day_sales,
       amount - LAG(amount) OVER (ORDER BY sale_date) AS daily_change
  FROM sales
 ORDER BY sale_date;
```

<h4>Агрегатные функции</h4>

- `SUM()` — сумма по столбцу в партиции
- `AVG()` — среднее значение столбца в партиции
- `COUNT()` — число строк в партиции
- `MIN()` — минимальное значение столбца в партиции
- `MAX()` — максимальное значение столбца в партиции

Пример (скользящее среднее за 3 дня):
```sql
SELECT date,
       sales,
       AVG(sales) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg_3d
  FROM daily_sales
 ORDER BY date;
```

Пример (кумулятивная сумма только для положительных продаж):
```sql
SELECT date,
       sales,
       SUM(CASE WHEN sales > 0 THEN sales ELSE 0 END)
       OVER (ORDER BY date) AS cumulative_positive_sales
  FROM daily_sales
 ORDER BY date;
```

Реализация `GROUP_CONCAT` в PostgreSQL:
```sql
-- Функция состояния для конкатенации строк
CREATE OR REPLACE FUNCTION concat_accum (state text, next_val text, delimiter text)
RETURNS text AS $$
BEGIN
    IF state IS NULL THEN
        RETURN next_val;
    ELSIF next_val IS NULL THEN
        RETURN state;
    ELSE
        RETURN state || delimiter || next_val;
    END IF;
END;
$$ LANGUAGE plpgsql;

-- Создаем агрегатную функцию с двумя параметрами (значение и разделитель)
CREATE AGGREGATE group_concat (text, text) (
    sfunc = concat_accum,
    stype = text,
    initcond = ''
);
```