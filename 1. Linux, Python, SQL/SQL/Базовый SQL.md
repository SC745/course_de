<h2>Базовый SQL</h2>
<h3>1. Введение</h3>

SQL, или язык структурированных запросов (Structured Query Language), представляет собой формальный язык, используемый для управления и взаимодействия с реляционными базами данных. SQL был разработан с целью упростить работу с данными в базах данных, обеспечивая стандартизированный способ выполнения операций, таких как запросы, вставка, обновление и удаление данных. SQL является декларативным языком, что означает, что пользователь указывает, что нужно сделать (например, какие данные извлечь), а не как это делать (как выполнить конкретный запрос). Он широко используется в различных типах приложений и является стандартом для работы с реляционными базами данных.

<h4>Порядок выполнения операций</h4>

Рассмотрим следующий запрос:
```sql
SELECT clients.first_name, clients.last_name, COUNT(0)
  FROM clients
       JOIN sales ON sales.id = sales.client_id
 WHERE clients.age > 50
 GROUP BY clients.first_name, clients.last_name
HAVING COUNT(0) > 10
 ORDER BY clients.first_name, clients.last_name
```

Порядок выполнения такого запроса будет выглядеть следующим образом:
1. `FROM` (выбор таблицы)
2. `JOIN` (комбинация с данными из другой таблицы)
3. `WHERE` (фильтрация)
4. `GROUP BY` (агрегирование)
5. `HAVING` (фильтрация агрегированных данных)
6. `SELECT` (возврат результата)
7. `ORDER BY` (сортировка).

<h3>2. Основные операторы</h3>

Операторы SQL разделяют на 4 группы в зависимости от специфики проводимых операций:
- Определения данных (Data Definition Language, DDL)
- Манипуляции данными (Data Manipulation Language, DML)
- Определения доступа к данным (Data Control Language, DCL)
- Управления транзакциями (Transaction Control Language, TCL)

<h4>DDL</h4>

Оператор `CREATE` - создание данных:
```sql
CREATE TABLE employee (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50),
    birthdate DATE,
    salary DECIMAL(10, 2)
);

CREATE TABLE department (
    department_id INT PRIMARY KEY,
    name VARCHAR(50)
);
```

Оператор `ALTER` - изменение данных:
```sql
ALTER TABLE employee ADD COLUMN department_id INT;
ALTER TABLE employee RENAME COLUMN first_name TO name;
ALTER TABLE employee ALTER COLUMN name TYPE VARCHAR(100);
ALTER TABLE employee DROP COLUMN last_name;

ALTER TABLE employee ADD CONSTRAINT fk_department
FOREIGN KEY (department_id) REFERENCES department(department_id)
ON DELETE SET NULL
ON UPDATE CASCADE;
```

Оператор `DROP` - удаление данных:
```sql
DROP TABLE employee;
DROP TABLE department;
```

<h4>DML</h4>

Оператор `INSERT` - вставка данных:
```sql
INSERT INTO department (name)
VALUES ('IT'), ('HR'), ('Sales');

INSERT INTO employee (name, birthdate, salary, department_id)
VALUES ('Ефим Родионов', '23-08-1986', 75000, 1),
       ('Макар Самойлов', '15-09-1993', 115000, 1),
       ('Вячеслав Титов', '30-11-1990', 85000, 1),
       ('Лилия Молчанова', '07-03-1997', 55000, 2),
       ('Дарья Смирнова', '19-05-1999', 60000, 2),
       ('Юлиана Кузьмина', '23-08-1988', 55000, 3),
       ('Герман Смирнов', '23-08-1989', 65000, 3),
       ('Владислав Шаров', '23-08-1992', 95000, 3)
```

Оператор `UPDATE` - обновление данных:
```sql
UPDATE employee AS emp
   SET salary = salary * 1.1
  FROM department AS dep
 WHERE dep.name = 'HR'
   AND birthdate BETWEEN '01-01-1990' AND '01-01-2000'
   AND dep.department_id = emp.department_id
```

Оператор `DELETE` - удаление данных:
```sql
DELETE FROM employee AS emp
 USING department AS dep
 WHERE dep.name = 'HR'
   AND dep.department_id = emp.department_id
```

Оператор `SELECT` - выбор данных:
```sql
SELECT emp.name,
       emp.salary,
       dep.name
  FROM employee AS emp
       JOIN (SELECT department_id,
                    max(salary) AS salary
               FROM employee
              GROUP BY department_id
             HAVING max(salary) > 70000) AS max_salaries
         ON emp.department_id = max_salaries.department_id
        AND emp.salary = max_salaries.salary
       JOIN department AS dep
         ON dep.department_id = emp.department_id
 WHERE dep.name <> 'HR'
 ORDER BY salary DESC, name ASC
 LIMIT 3
```

<h4>DCL</h4>

DCL включает команды для управления правами доступа к данным и структурам базы данных. Разделяют различные уровни доступа, типы прав и сущности, которым могут предоставляться права. Основными операторами являются `GRANT` (предоставление прав), `REVOKE` (отзыв прав) и `DENY` (явный запрет):
```sql
GRANT privilege_name ON object TO user_or_role;
REVOKE privilege_name ON object FROM user_or_role;
DENY privilege_name ON object TO user_or_role;
```

Пользователи и роли:
```sql
-- Операции с пользователем
CREATE USER auditor WITH PASSWORD 'audit123';
ALTER USER developer WITH PASSWORD 'new_pass';
DROP USER temp_user;

-- Создание ролей
CREATE ROLE read_only_role;
CREATE ROLE hr_manager_role;
CREATE ROLE data_analyst_role;

-- Назначение ролей пользователям
GRANT read_only_role TO john_doe;
GRANT hr_manager_role, data_analyst_role TO jane_smith;
```

Возможные типы прав:
- `SELECT`: чтение данных
- `INSERT`: вставка новых данных
- `UPDATE`: обновление данных (может быть ограничено столбцами)
- `DELETE`: удаление данных
- `REFERENCES`: возможность создавать внешние ключи на таблицу
- `TRIGGER`: возможность создавать триггеры на таблице
- `CREATE`, CONNECT, TEMP: на уровне базы данных/схемы
- `USAGE`: на использование последовательностей, доменов, схем и т.д.
- `ALL`: все права

Уровни доступа:
1. Уровень базы данных
```sql
GRANT CONNECT ON DATABASE company_db TO app_user;
GRANT CREATE ON DATABASE company_db TO developer;
```
2. Уровень схемы
```sql
GRANT USAGE ON SCHEMA public TO user1;
GRANT CREATE ON SCHEMA hr_schema TO hr_manager;
```
3. Уровень таблицы
```sql
GRANT SELECT, INSERT ON employees TO manager;
GRANT ALL PRIVILEGES ON departments TO admin;
```
4. Уровень столбца
```sql
GRANT SELECT (id, name, email) ON employees TO support;
GRANT UPDATE (phone, address) ON customers TO operator;
```
5. Уровень строки (через RLS - Row Level Security)
```sql
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
CREATE POLICY user_orders_policy ON orders
FOR ALL USING (user_id = current_user_id());
```

<h4>TCL</h4>

Операторы:
- `COMMIT` - подтверждение транзакции
- `ROLLBACK` - откат транзакции
- `SAVEPOINT` - точка сохранения
- `SET TRANSACTION` - настройка транзакции


```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

BEGIN;
INSERT INTO department (name) VALUES ('Accounting');
SAVEPOINT dep_created;
UPDATE employee SET department_id = 4 WHERE id = 1;
COMMIT;
```

<h3>3. Работа со множествами</h3>

В SQL есть возможность применять операции работы со множествами для запросов, результаты которых имеют одинаковый набор столбцов. Для примеров будут использоваться запросы типа `SELECT UNNEST(ARRAY[...]) AS col`, позволяющие выбирать значения в виде столбца (PostgreSQL).

<h4>UNION [ALL]</h4>

Позволяет объединять результаты запросов, оставляя уникальные значения:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) AS col
UNION
SELECT UNNEST(ARRAY[1,1,2,3]) AS col
ORDER BY col
-- [1,2,3,4]
```

При добавлении `ALL` предотвращает удаление дубликатов:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) AS col
UNION ALL
SELECT UNNEST(ARRAY[1,1,2,3]) AS col
ORDER BY col
-- [1,1,1,1,1,2,3,3,4]
```

<h4>EXCEPT [ALL]</h4>

Удаляет все значения второго запроса из первого запроса:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) as col
EXCEPT
SELECT UNNEST(ARRAY[1,1,2,3]) as col
ORDER BY col
-- [4]
```

При добавлении `ALL` удаляет не все значения, а то количество, которое есть во втором запросе:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) as col
EXCEPT ALL
SELECT UNNEST(ARRAY[1,1,2,3]) as col
ORDER BY col
-- [1,4]
```

<h4>INTERSECT [ALL]</h4>

Возвращает те значения, которые есть в обоих запросах:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) as col
INTERSECT
SELECT UNNEST(ARRAY[1,1,2,3]) as col
ORDER BY col
-- [1,3]
```

При добавлении `ALL` возвращает значения с учетом их количества в обоих запросах:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) as col
INTERSECT ALL
SELECT UNNEST(ARRAY[1,1,2,3]) as col
ORDER BY col
-- [1,1,3]
```

`INTERSECT` является более приоритетной операцией, чем `UNION` и `EXCEPT`:
```sql
SELECT UNNEST(ARRAY[1,1,1,3,4]) as col
UNION
SELECT UNNEST(ARRAY[1,1,2,3]) as col
INTERSECT
SELECT UNNEST(ARRAY[1,1,1,3]) as col
ORDER BY col
-- [1,3,4], а не [1,3]
```

Для задания явной последовательности операций можно использовать скобки.

<h3>4. Соединения таблиц</h3>

Соединения используются для объединения строк из двух или более таблиц на основе определенного условия.

<h4>INNER JOIN</h4>

Возвращает только те строки, для которых условие выполняется.
```sql
SELECT emp.name, dep.name
  FROM employee AS emp
       INNER JOIN department AS dep
       ON emp.department_id = dep.department_id
```

<h4>LEFT JOIN</h4>

Возвращает все строки из левой таблицы и только те строки из правой, для которых условие выполняется.
```sql
SELECT emp.name, dep.name
  FROM employee AS emp
       LEFT JOIN department AS dep
       ON emp.department_id = dep.department_id
```

<h4>RIGHT JOIN</h4>

Возвращает все строки из правой таблицы и только те строки из левой, для которых условие выполняется.
```sql
SELECT emp.name, dep.name
  FROM department AS dep
       RIGHT JOIN employee AS emp
       ON emp.department_id = dep.department_id
```

<h4>FULL JOIN</h4>

Возвращает все строки из обеих таблиц. Если есть совпадение, то строки объединяются, если нет, то недостающие части заполняются `NULL`.
```sql
SELECT emp.name, dep.name
  FROM employee AS emp
       FULL JOIN department AS dep
       ON emp.department_id = dep.department_id
```

<h4>CROSS JOIN</h4>

Возвращает все возможные комбинации строк двух таблиц.
```sql
SELECT emp.name, dep.name
  FROM employee AS emp
       CROSS JOIN department AS dep
```

<h4>Специальные возможности</h4>

Использование `USING` для соединения таблиц, в которых столбцы, участвующие в условии, имеют одинаковые названия и условием является равенство:
```sql
SELECT employee.name, department.name
  FROM employee
       JOIN department USING (department_id)
```

Также в таких случаях можно использовать `NATURAL JOIN`:
```sql
SELECT employee.name, department.name
  FROM employee
       NATURAL JOIN department
```

`SELF JOIN` - соединение таблицы с самой собой:
```sql
SELECT t1.name, t2.name
  FROM employee AS t1
       JOIN employee AS t2
       ON t1.employee_id = t2.employee_id + 1
```

<h3>5. Подзапросы</h3>

Подзапрос - это запрос, вложенный в другой запрос. Подзапросы могут использоваться в различных частях основного запроса и решают широкий спектр задач.

Классификация подзапросов:
- По количеству возвращаемых строк и столбцов:
  - Скалярные (одна строка, один столбец)
  - Векторные (много строк, один столбец)
  - Табличные (много строк, много столбцов)
- По зависимости от внешнего запроса:
  - Независимые (некоррелированные) — выполняются один раз
  - Зависимые (коррелированные) — выполняются для каждой строки внешнего запроса
- По месту использования:
  - В `SELECT` (скалярные подзапросы)
  - В `FROM` (все типы)
  - В `WHERE` (скалярные и векторные)
  - В `HAVING` (скалярные и векторные)

Подзапрос в `SELECT`:
```sql
SELECT name,
       salary,
       salary - (SELECT AVG(salary) FROM employee) as diff_from_avg
  FROM employee;
```

Подзапрос в `FROM`:
```sql
SELECT dept_stats.name,
       dept_stats.avg_salary
  FROM (SELECT department.name,
               AVG(salary) as avg_salary
          FROM employee
               JOIN department USING (department_id)
         GROUP BY department.name) AS dept_stats
WHERE dept_stats.avg_salary > 50000;
```

Подзапрос в `WHERE` (аналогично с `HAVING`):

```sql
-- Использование IN
SELECT name
  FROM employee
 WHERE department_id IN
       (SELECT department_id
          FROM department
         WHERE budget > 100000);

-- Аналог с EXISTS
SELECT name
  FROM employee AS emp
 WHERE EXISTS
       (SELECT 0
          FROM department AS dep
         WHERE budget > 100000
           AND emp.department_id = dep.department_id);
```

<h4>ANY</h4>

Оператор `ANY` возвращает `TRUE`, если условие выполняется хотя бы для одного значения возвращенного подзапросом. Можно также использовать его синоним `SOME`, однако чаще в запросах используют все же первый вариант оператора.
- значение >= `ANY` (подзапрос) возвращает `TRUE` если значение левого операнда больше либо равно минимальному значению, возвращаемому подзапросом правого операнда.
- значение = `ANY` (подзапрос) возвращает `TRUE` если значение левого операнда равно хотя бы одному из значений, возвращаемому подзапросом правого операнда.
- значение <= `ANY` (подзапрос) возвращает `TRUE` если значение левого операнда меньше либо равно максимальному значению, возвращаемому подзапросом правого операнда.
- значение != `ANY` (подзапрос) возвращает `TRUE` если значение левого операнда не равно ни одному из значений, возвращаемому подзапросом правого операнда.

```sql
SELECT name
  FROM employee
 WHERE salary >= ANY
       (SELECT salary
          FROM employee
               JOIN department USING (department_id)
         WHERE department.name = 'HR')
```

<h4>ALL</h4>

Оператор `ALL` возвращает `TRUE`, если условие выполняется для всех значений возвращенных подзапросом.
- значение >= `ALL` (подзапрос) - возвращает `TRUE` если значение левого операнда больше либо равно максимальному значению, возвращаемому подзапросом правого операнда.
- значение = `ALL` (подзапрос) - возвращает `TRUE` если значение левого операнда равно каждому значению, возвращаемого подзапросом правого операнда.
- значение <= `ALL` (подзапрос) - возвращает `TRUE` если значение левого операнда меньше либо равно минимальному значению, возвращаемому подзапросом правого операнда.
- значение != `ALL` (подзапрос) - возвращает `TRUE` если значение левого операнда не равно ни одному из значений, возвращаемому подзапросом правого операнда.

```sql
SELECT name
  FROM employee
 WHERE salary >= ALL
       (SELECT salary
          FROM employee
               JOIN department USING (department_id)
         WHERE department.name = 'HR')
```

<h3>6. Основные функции</h3>

<h4>Работа с NULL</h4>

Проверка осуществляется с помощью ключевого слова `IS`:
```sql
SELECT name, department_id
  FROM employee
 WHERE department_id IS NULL
```

Для подстановки значений вместо NULL используется функция `COALESCE`:
```sql
SELECT name, COALESCE(department_id, 'Не определен') AS department_id
  FROM employee
 WHERE department_id IS NULL
```

Агрегатные функции обрабатывают `NULL` по разному:
- `COUNT` - `COUNT(col)` инорирует `NULL`, `COUNT(*)` считает `NULL`
- `MIN`, `MAX`, `AVG`, `SUM` - игнорируют `NULL`
- `GROUP BY` - формирует новую группу с `NULL`

<h4>LIKE и RLIKE</h4>

LIKE используется для поиска строки по шаблону:
- `_` - один символ
- `%` - последовательность символов

```sql
SELECT name
  FROM department
 WHERE name LIKE '_a%' -- Sales
```

RLIKE используется для поиска строки по регулярному выражению. Синтаксис регулярных выражений:
- Специальные символы:
  - `^` - Начало строки (`'^A'` - начинается с `A`)
  - `$` - Конец строки (`'com$'` - заканчивается на `com`)
  - `.` - Любой символ (`'^A.*'` - начинается с `A`, затем любые символы)
  - `*` - 0 или более повторений (`'ab*c'` - `ac`, `abc`, `abbc`, `abbbc`)
  - `+` - 1 или более повторений (`'ab+c'` - `abc`, `abbc`, `abbbc` (не `ac`))
  - `?` - 0 или 1 повторение (`'colou?r'` - `color`, `colour`)
  - `{n}` - Точное количество (`'a{3}'` - `aaa`)
  - `{n,}` - n или более (`'a{2,}'` - `aa`, `aaa`, `aaaa`)
  - `{n,m}` - От n до m (`'a{2,4}'` - `aa`, `aaa`, `aaaa`)
- Классы символов:
  - `[abc]` - Любой из `a`, `b`, `c` (`'[aeiou]'` - любая гласная)
  - `[a-z]` - Любая строчная буква (`'[a-z]+'` - слово из строчных букв)
  - `[A-Z]` - Любая заглавная буква (`'^[A-Z]'` - начинается с заглавной)
  - `[0-9]` - Любая цифра (`'[0-9]{3}'` - три цифры подряд)
  - `[^abc]` - Любой символ, кроме `a`, `b`, `c` (`'[^0-9]'` - не цифра)
  - `\d` - Цифра (`'\d+'` - одна или более цифр)
  - `\D` - Не цифра (`'\D+'` - один или более не-цифр)
  - `\w` - Буква, цифра или `_` (`'\w+'` - слово)
  - `\W` - Не буква, не цифра, не `_` (`'\W'` - символ не слова)
  - `\s` - Пробельный символ (`'\s+'` - пробелы)
  - `\S` - Не пробельный символ (`'\S+'` - непробельные символы)
  - `\b` - Граница слова (`'\\bword\\b'` - отдельное слово)

```sql
-- Имена, содержащие 'john' или 'jane'
SELECT first_name 
FROM employees 
WHERE first_name RLIKE 'john|jane';

-- Номера телефонов в форматах: 123-456-7890 или (123) 456-7890
SELECT phone 
FROM contacts 
WHERE phone RLIKE '^[0-9]{3}-[0-9]{3}-[0-9]{4}$|^\\([0-9]{3}\\) [0-9]{3}-[0-9]{4}$';
```

<h4>CASE WHEN и IF</h4>

Условные конструкции `CASE WHEN` и `IF` позволяют выполнять различную логику в зависимости от условий прямо в SQL запросах.

```sql
-- Базовый синтаксис
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    ELSE default_result
END

-- Простая форма
CASE expression
    WHEN value1 THEN result1
    WHEN value2 THEN result2
    ...
    ELSE default_result
END

-- IF
IF(condition, value_if_true, value_if_false)
```

<h4>Работа со строками</h4>

- Конкатенация:
  - `CONCAT(str1, str2)` - конкатенация
  - `CONCAT_WS(del, str1, str2)` - конкатенация с разделителем
  - `REPEAT(str, num)` - конкатенация `str` `num` раз
  - `GROUP_CONCAT(col)` - конкатенация с разделителем по группе
- Получение подстроки:
  - `LEFT(str, num)` - подстрока слева длиной `num`
  - `RIGHT(str, num)` - подстрока справа длиной `num`
  - `SUBSTRING(start, end)` - подстрока по индексам
- Удаление пробелов:
  - `TRIM(BOTH|LEADING|TRAILING chr FROM str)` - удаление символов по условию
  - `LTRIM(str)` - удаление пробелов слева
  - `RTRIM(str)` - удаление пробелов справа
- Изменение регистра:
  - `UPPER(str)` - верхний регистр
  - `LOWER(str)` - нижний регистр
  - `INITCAP(str)` - первая буква заглавная
- Заполнение символами:
  - `LPAD(str, num, chr)` - дополнение `str` до длины `num` символами `chr` слева
  - `RPAD(str, num, chr)` - дополнение `str` до длины `num` символами `chr` справа
- Специальные:
  - `LENGTH(str)` - длина строки
  - `LOCATE(chr, str, num)` - поиск `chr` в `str` с позиции `num`
  - `REPLACE(str, substr1, substr2)` - замена `substr1` на `substr2` в `str`
  - `REVERSE(str)` - обратный порядок

<h4>Работа с датами</h4>

- Получение текущей даты и времени:
  - `CURRENT_TIMESTAMP` - текущая дата и время
  - `CURRENT_DATE` - текущая дата
  - `CURRENT_TIME` - текущее время
- Получение элементов даты:
  - `YEAR(date)` - год
  - `MONTH(date)` - месяц
  - `DAY(date)` - день месяца
  - `HOUR(time)` - час
  - `MINUTE(time)` - минута
  - `SECOND(time)` - секунда
  - `DAYOFWEEK(date)` - день недели
  - `WEEK(date)` - неделя года
  - `QUARTER(date)` - квартал
  - `EXTRACT(unit FROM DATE|TIMESTAMP date|timestamp)` - извлечь `unit` (`YEAR`,`MONTH`...) из `date` или `timestamp`
- Операции с датами:
  - `DATE_ADD(date, INTERVAL num unit)` - прибавить к дате `num` единиц времени `unit` (`YEAR`,`MONTH`...)
  - `DATE_SUB(date, INTERVAL num unit)` - отнять от даты `num` единиц времени `unit` (`YEAR`,`MONTH`...)
  - `DATEDIFF(date2, date1)` = разница в днях между датами
  - `TIMESTAMPDIFF(unit, timestamp1, timestamp2)` - разница в `unit` между датами и временем
- Работа с временными зонами:
  - `UTC_TIMESTAMP()` - текущая дата и время в UTC
  - `UTC_DATE()` - текущая дата в UTC
  - `UTC_TIME()` - текущее время в UTC
  - `CONVERT_TZ(timestamp, tz_str1, tz_str2)` - изменить временную зону (`tz_str`:`'+00:00'`)

<h3>7. Табличные выражения</h3>

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

<h3>8. Использование переменных</h3>

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
cursor.execute("INSERT INTO users (name, age) VALUES (%(name)s, %(age)s)", data)
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

<h3>9. MERGE, REPLACE, UPSERT</h3>

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

<h3>10. Оконные функции</h3>

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