# devops-DZ6.4
# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

## Ответ

Подготовим docker-compose.yaml

```yaml
version: '3.5'
services:
  postgres:
    image: postgres:13
    environment:
      - POSTGRES_PASSWORD=postgres
      - POSTGRES_USER=postgres
    volumes:
      - ./dbdata:/var/lib/postgresql/data
      - ./dbbackup:/data/backup/postgres
    ports:
      - "5432:5432"
    restart: always
```

```bash
vk@vkvm:~/DZ6.4$ docker-compose up -d
Starting dz64_postgres_1 ... done
```
Подключитесь к БД PostgreSQL используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

```bash
vk@vkvm:~/DZ6.4$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS              PORTS                                                  NAMES
0775693fc7d8   postgres:13   "docker-entrypoint.s…"   7 minutes ago   Up About a minute   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp              dz64_postgres_1
7c6b8ca5a695   mysql:8       "docker-entrypoint.s…"   10 days ago     Up 3 minutes        0.0.0.0:3306->3306/tcp, :::3306->3306/tcp, 33060/tcp   dz63_mysql_1
vk@vkvm:~/DZ6.4$ docker exec -it 0775693fc7d8 /bin/bash
root@0775693fc7d8:/# psql -h localhost -p 5432 -U postgres -W
Password: 
psql (13.8 (Debian 13.8-1.pgdg110+1))
Type "help" for help.
```

**Найдите и приведите** управляющие команды для:
- вывода списка БД
```bash
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
```bash
postgres=# \c postgres
Password: 
You are now connected to database "postgres" as user "postgres".
```

- вывода списка таблиц
```bash
postgres=# \dt
Did not find any relations.
postgres=# \dtS
                    List of relations
   Schema   |          Name           | Type  |  Owner   
------------+-------------------------+-------+----------
 pg_catalog | pg_aggregate            | table | postgres
 pg_catalog | pg_am                   | table | postgres
 pg_catalog | pg_amop                 | table | postgres
 pg_catalog | pg_amproc               | table | postgres
 pg_catalog | pg_attrdef              | table | postgres
 pg_catalog | pg_attribute            | table | postgres
 pg_catalog | pg_auth_members         | table | postgres
 pg_catalog | pg_authid               | table | postgres
 pg_catalog | pg_cast                 | table | postgres
 pg_catalog | pg_class                | table | postgres
 pg_catalog | pg_collation            | table | postgres
 pg_catalog | pg_constraint           | table | postgres
 pg_catalog | pg_conversion           | table | postgres
 pg_catalog | pg_database             | table | postgres
 pg_catalog | pg_db_role_setting      | table | postgres
 pg_catalog | pg_default_acl          | table | postgres
 pg_catalog | pg_depend               | table | postgres
 pg_catalog | pg_description          | table | postgres
 pg_catalog | pg_enum                 | table | postgres
 pg_catalog | pg_event_trigger        | table | postgres
 pg_catalog | pg_extension            | table | postgres
 pg_catalog | pg_foreign_data_wrapper | table | postgres
 pg_catalog | pg_foreign_server       | table | postgres
 pg_catalog | pg_foreign_table        | table | postgres
 pg_catalog | pg_index                | table | postgres
 pg_catalog | pg_inherits             | table | postgres
 pg_catalog | pg_init_privs           | table | postgres
 pg_catalog | pg_language             | table | postgres
 pg_catalog | pg_largeobject          | table | postgres
 pg_catalog | pg_largeobject_metadata | table | postgres
 pg_catalog | pg_namespace            | table | postgres
 pg_catalog | pg_opclass              | table | postgres
 pg_catalog | pg_operator             | table | postgres
 pg_catalog | pg_opfamily             | table | postgres
 pg_catalog | pg_partitioned_table    | table | postgres
 pg_catalog | pg_policy               | table | postgres
 pg_catalog | pg_proc                 | table | postgres
 pg_catalog | pg_publication          | table | postgres
 pg_catalog | pg_publication_rel      | table | postgres
 pg_catalog | pg_range                | table | postgres
 pg_catalog | pg_replication_origin   | table | postgres
 pg_catalog | pg_rewrite              | table | postgres
 pg_catalog | pg_seclabel             | table | postgres
 pg_catalog | pg_sequence             | table | postgres
 pg_catalog | pg_shdepend             | table | postgres
 pg_catalog | pg_shdescription        | table | postgres
 pg_catalog | pg_shseclabel           | table | postgres
 pg_catalog | pg_statistic            | table | postgres
 pg_catalog | pg_statistic_ext        | table | postgres
 pg_catalog | pg_statistic_ext_data   | table | postgres
 pg_catalog | pg_subscription         | table | postgres
 pg_catalog | pg_subscription_rel     | table | postgres
 pg_catalog | pg_tablespace           | table | postgres
 pg_catalog | pg_transform            | table | postgres
 pg_catalog | pg_trigger              | table | postgres
 pg_catalog | pg_ts_config            | table | postgres
 pg_catalog | pg_ts_config_map        | table | postgres
 pg_catalog | pg_ts_dict              | table | postgres
 pg_catalog | pg_ts_parser            | table | postgres
 pg_catalog | pg_ts_template          | table | postgres
 pg_catalog | pg_type                 | table | postgres
 pg_catalog | pg_user_mapping         | table | postgres
(62 rows)
```
- вывода описания содержимого таблиц
```bash
postgres=# \d pg_database
               Table "pg_catalog.pg_database"
    Column     |   Type    | Collation | Nullable | Default 
---------------+-----------+-----------+----------+---------
 oid           | oid       |           | not null | 
 datname       | name      |           | not null | 
 datdba        | oid       |           | not null | 
 encoding      | integer   |           | not null | 
 datcollate    | name      |           | not null | 
 datctype      | name      |           | not null | 
 datistemplate | boolean   |           | not null | 
 datallowconn  | boolean   |           | not null | 
 datconnlimit  | integer   |           | not null | 
 datlastsysoid | oid       |           | not null | 
 datfrozenxid  | xid       |           | not null | 
 datminmxid    | xid       |           | not null | 
 dattablespace | oid       |           | not null | 
 datacl        | aclitem[] |           |          | 
Indexes:
    "pg_database_datname_index" UNIQUE, btree (datname), tablespace "pg_global"
    "pg_database_oid_index" UNIQUE, btree (oid), tablespace "pg_global"
Tablespace: "pg_global"
```
- выхода из psql
```bash
postgres=# \q
root@0775693fc7d8:/# 
```
## Задача 2

Используя `psql` создайте БД `test_database`.
```bash
postgres=# CREATE DATABASE test_database;
CREATE DATABASE
```
Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/master/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.
```bash
vk@vkvm:~/DZ6.4$ sudo mv test_dump.sql dbbackup/

root@023ece8e96f1:/# psql -U postgres test_database < /data/backup/postgres/test_dump.sql
root@0775693fc7d8:/# psql -U postgres test_database < /data/backup/postgres/test_dump.sql
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
Перейдите в управляющую консоль `psql` внутри контейнера.
```bash
postgres=# \l
                                   List of databases
     Name      |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
---------------+----------+----------+------------+------------+-----------------------
 postgres      | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 template1     | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
               |          |          |            |            | postgres=CTc/postgres
 test_database | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)

```
Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.
```bash
postgres=# \c test_database 
Password: 
You are now connected to database "test_database" as user "postgres".
test_database=# \d
              List of relations
 Schema |     Name      |   Type   |  Owner   
--------+---------------+----------+----------
 public | orders        | table    | postgres
 public | orders_id_seq | sequence | postgres
(2 rows)

test_database=# analyze VERBOSE public.orders ;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.
```bash
test_database=# SELECT tablename, attname, avg_width FROM pg_stats WHERE avg_width IN (SELECT MAX(avg_width) FROM pg_stats WHERE tablename = 'orders') and tablename = 'orders';
 tablename | attname | avg_width 
-----------+---------+-----------
 orders    | title   |        16
(1 row)
```
## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Для шардинга таблиц мы делаем следующие шаги:

1) Переименовываем текущую таблицу в orders_old
2) Создаем пустую таблицу с исходным именем orders
3) Создаем две пустые таблицы orders_1 и orders_2 с наследованием от orders
4) Определяем в этих таблицах ограничения на значение ключа price
5) Определяем правила, при котором INSERT в таблицу orders будет заменяться на INSERT в соответствующую дочернюю таблицу (в зависимости от значения ключа price)
6) Копируем через INSERT таблицу orders_old в orders
SQL транзакция
```bash
test_database=# ALTER TABLE orders RENAME TO orders_old;
ALTER TABLE
test_database=# CREATE TABLE orders AS table orders_old WITH NO DATA;
CREATE TABLE AS
test_database=# CREATE TABLE orders_1 (
    CHECK (price > 499)
) INHERITS (orders);

CREATE TABLE orders_2 (
    CHECK (price <= 499)
) INHERITS (orders);

CREATE RULE orders_1_insert AS
ON INSERT TO orders WHERE
    (price > 499)
DO INSTEAD
    INSERT INTO orders_1 VALUES (NEW.*);
       
CREATE RULE orders_2_insert AS
ON INSERT TO orders WHERE
    (price <= 499)
DO INSTEAD
    INSERT INTO orders_2 VALUES (NEW.*);
CREATE TABLE
CREATE TABLE
CREATE RULE
CREATE RULE
test_database=# begin;
BEGIN
test_database=*# INSERT INTO orders SELECT * FROM orders_old;
INSERT 0 0
test_database=*# commit;
COMMIT
```

```bash
test_database=# \d
              List of relations
 Schema |     Name      |   Type   |  Owner   
--------+---------------+----------+----------
 public | orders        | table    | postgres
 public | orders_1      | table    | postgres
 public | orders_2      | table    | postgres
 public | orders_id_seq | sequence | postgres
 public | orders_old    | table    | postgres
(5 rows)

test_database=# TABLE orders_1;
 id |       title        | price 
----+--------------------+-------
  2 | My little database |   500
  6 | WAL never lies     |   900
  8 | Dbiezdmin          |   501
(3 rows)

test_database=# TABLE orders_2;
 id |        title         | price 
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  7 | Me and my bash-pet   |   499
(5 rows)

test_database=# TABLE orders;
 id |        title         | price 
----+----------------------+-------
  2 | My little database   |   500
  6 | WAL never lies       |   900
  8 | Dbiezdmin            |   501
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  7 | Me and my bash-pet   |   499
(8 rows)
```

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

```sql
CREATE TABLE orders (
    id integer NOT NULL,
    title character varying(80) NOT NULL,
    price integer DEFAULT 0
) PARTITION BY RANGE (price);
```
Далее создаем секции orders_1 и orders_2 с указанными ограничениями
```sql
CREATE TABLE orders_1 PARTITION OF orders
    FOR VALUES GREATER THAN ('499');

CREATE TABLE orders_2 PARTITION OF orders
    FOR VALUES FROM ('0') TO ('499');
```

## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```bash
root@0775693fc7d8:/# pg_dump -U postgres -v -f /data/backup/postgres/test_database.sql
pg_dump: last built-in OID is 16383
pg_dump: reading extensions
pg_dump: identifying extension members
pg_dump: reading schemas
pg_dump: reading user-defined tables
pg_dump: reading user-defined functions
pg_dump: reading user-defined types
pg_dump: reading procedural languages
pg_dump: reading user-defined aggregate functions
pg_dump: reading user-defined operators
pg_dump: reading user-defined access methods
pg_dump: reading user-defined operator classes
pg_dump: reading user-defined operator families
pg_dump: reading user-defined text search parsers
pg_dump: reading user-defined text search templates
pg_dump: reading user-defined text search dictionaries
pg_dump: reading user-defined text search configurations
pg_dump: reading user-defined foreign-data wrappers
pg_dump: reading user-defined foreign servers
pg_dump: reading default privileges
pg_dump: reading user-defined collations
pg_dump: reading user-defined conversions
pg_dump: reading type casts
pg_dump: reading transforms
pg_dump: reading table inheritance information
pg_dump: reading event triggers
pg_dump: finding extension tables
pg_dump: finding inheritance relationships
pg_dump: reading column info for interesting tables
pg_dump: flagging inherited columns in subtables
pg_dump: reading indexes
pg_dump: flagging indexes in partitioned tables
pg_dump: reading extended statistics
pg_dump: reading constraints
pg_dump: reading triggers
pg_dump: reading rewrite rules
pg_dump: reading policies
pg_dump: reading row-level security policies
pg_dump: reading publications
pg_dump: reading publication membership
pg_dump: reading subscriptions
pg_dump: reading large objects
pg_dump: reading dependency data
pg_dump: saving encoding = UTF8
pg_dump: saving standard_conforming_strings = on
pg_dump: saving search_path = 
pg_dump: implied data-only restore
```
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

Добавить ограничение `UNIQUE`
```sql
CREATE TABLE public.orders (
    id integer NOT NULL,
    title character varying(80) NOT NULL UNIQUE,
    price integer DEFAULT 0
);
```

