## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.
```
docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol3:/var/lib/postgresql postgres:13
```

Подключитесь к БД PostgreSQL используя `psql`.

```
psql -U postgres -h 192.168.1.149 -p 5432
```

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:
- вывода списка БД

```
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
(3 rows)

```

- подключения к БД
```
psql -U postgres -h 192.168.1.149 -p 5432```

```
- вывода списка таблиц
```
postgres=# \dt
Did not find any relations.

```

```
 \dtS
                   List of relations
   Schema   |          Name           | Type  |  Owner
------------+-------------------------+-------+----------
 pg_catalog | pg_aggregate            | table | postgres
 pg_catalog | pg_am                   | table | postgres
 pg_catalog | pg_amop                 | table | postgres
 pg_catalog | pg_amproc               | table | postgres
 pg_catalog | pg_attrdef              | table | postgres

```
- вывода описания содержимого таблиц
```
postgres=# \dtS+ pg_am
                      List of relations
   Schema   | Name  | Type  |  Owner   | Size  | Description
------------+-------+-------+----------+-------+-------------
 pg_catalog | pg_am | table | postgres | 40 kB |
(1 row)


```
- выхода из psql
```

\q

```

## Задача 2

Используя `psql` создайте БД `test_database`.
```
CREATE DATABASE test_database;
```


Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.
```
docker exec -it pg-docker bash
```
```
root@84f8e65948da:/# cd /var/lib/postgresql/data
root@84f8e65948da:/var/lib/postgresql/data# psql -U postgres -f ./test_dump.sql test_database
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE


```

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
```
test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```

Используя таблицу [pg_stats](), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

```
test_database=# select avg_width from pg_stats where tablename='orders';
 avg_width
-----------
         4
        16
         4
(3 rows)
```

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Предложите SQL-транзакцию для проведения данной операции.
```
test_database=# create table orders (id integer, title varchar(80), price integer) partition by range(price);
CREATE TABLE
test_database=# create table orders_1 partition of orders for values from (0) to (499);
CREATE TABLE
test_database=# create table orders_1 partition of orders for values from (499) to (999999999);
CREATE TABLE
test_database=# insert into orders (id, title, price) select * from orders_simple;
INSERT 0 8
test_database=# \dt
             List of relations
 Schema |      Name      | Type  |  Owner
--------+----------------+-------+----------
 public | orders         | table | postgres
 public | orders_1	 | table | postgres
 public | orders_2	 | table | postgres
 public | orders_simple  | table | postgres
(4 rows)
```

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?
```
При проектировании можно было сделать таблицу секционированной, тогда не потребовалось бы переименовывать исходную таблицу и переносить данные в новую.
```
## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```
pg_dump -U postgres -d test_database >test_database_dump.sql

```

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

```
Для добавления уникальности можно создать индекс или первичный ключ.
CREATE INDEX ON orders ((lower(title)));
```
