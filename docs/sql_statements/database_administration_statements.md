Table of Contents
=================

   * [Database Administration Statements](#database-administration-statements)
      * [CHECKSUM](#checksum)
         * [CHECKSUM TABLE](#checksum-table)
      * [SET](#set)
      * [SHOW](#show)
         * [SHOW ENGINES](#show-engines)
         * [SHOW DATABASES](#show-databases)
         * [SHOW TABLES](#show-tables)
         * [SHOW TABLE STATUS](#show-table-status)
         * [SHOW COLUMNS](#show-columns)
         * [SHOW CREATE TABLE](#show-create-table)
         * [SHOW PROCESSLIST](#show-processlist)
         * [SHOW VARIABLES](#show-variables)
      * [KILL](#kill)
         * [KILL processlist_id](#kill-processlist_id)
      * [USE](#use)
         * [USE DATABASE](#use-database)

# Database Administration Statements

## CHECKSUM

### CHECKSUM TABLE

`Syntax`
```
CHECKSUM TABLE  [database_name.]table_name
```

`Instructions`
* Reports a checksum for the contents of a table
* RadonDB gives same result as MySQL

`Example: `

```
mysql> checksum table test.t1;
+----------+------------+
| Table    | Checksum   |
+----------+------------+
| test.t1  | 2464930879 |
+----------+------------+
1 row in set (0.00 sec)
```

## SET

`Instructions`
* For compatibility JDBC/mydumper
* SET is an empty operation, *all operations will not take effect*, do not use it directly。
## SHOW

### SHOW ENGINES

`Syntax`
```
SHOW ENGINES
```

`Instructions`
* Backend partitioned supported engine list by MySQL

`Example: `
```

mysql> SHOW ENGINES;
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                                    | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
| MyISAM             | YES     | MyISAM storage engine                                                      | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                                      | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Percona-XtraDB, Supports transactions, row-level locking, and foreign keys | YES          | YES  | YES        |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears)             | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                                         | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                                         | NO           | NO   | NO         |
| ARCHIVE            | YES     | Archive storage engine                                                     | NO           | NO   | NO         |
| TokuDB             | YES     | Percona TokuDB Storage Engine with Fractal Tree(tm) Technology             | YES          | YES  | YES        |
| FEDERATED          | NO      | Federated MySQL storage engine                                             | NULL         | NULL | NULL       |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables                  | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------------------+--------------+------+------------+
10 rows in set (0.00 sec)
```

### SHOW DATABASES

`Syntax`
```
SHOW DATABASES
```

`Instructions`
* Including system DB, such as mysql, information_schema

`Example: `
```
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| db_gry_test        |
| db_test1           |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.01 sec)
```

### SHOW TABLES

`Syntax`
```
SHOW [FULL] TABLES
[FROM db_name]
[LIKE 'pattern' | WHERE expr]
```

`Instructions`
* If db_name is not specified, the table under the current DB is returned

`Example: `
```
mysql> SHOW TABLES;
+--------------------+
| Tables_in_db_test1 |
+--------------------+
| t1                 |
| t2                 |
+--------------------+
2 rows in set (0.01 sec)
```

### SHOW TABLE STATUS

`Syntax`
```
SHOW TABLE STATUS
[FROM db_name]
```

`Instructions`
* If db_name is not specified, the table under the current DB is returned

`Example: `
```
mysql> show table status;
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
| Name   | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation       | Checksum | Create_options | Comment |
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
| b      | InnoDB |      10 | Dynamic    |    6 |          16384 |       16384 |               0 |            0 |         0 |           NULL | 2018-12-24 08:26:24 | 2019-01-22 08:31:47 | NULL       | utf8_general_ci |     NULL |                |         |
| g      | InnoDB |      10 | Dynamic    |    1 |          16384 |       16384 |               0 |            0 |         0 |           NULL | 2018-12-24 08:26:24 | 2019-02-28 03:20:46 | NULL       | utf8_general_ci |     NULL |                          |         |
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+-----------------+----------+----------------+---------+
2 rows in set (0.08 sec)
```

### SHOW COLUMNS

`Syntax`

```
SHOW [FULL] {COLUMNS | FIELDS} 
FROM [db_name.]table_name
[LIKE 'pattern' | WHERE expr]
```

`Instructions`
* Get the column definitions of a table

`Example: `

```
mysql> CREATE TABLE t1(A INT PRIMARY KEY, B VARCHAR(10)) PARTITION BY HASH(A);
Query OK, 0 rows affected (0.52 sec)

mysql> SHOW COLUMNS FROM t1;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| A     | int(11)     | NO   | PRI | NULL    |       |
| B     | varchar(10) | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
2 rows in set (0.03 sec)

mysql> SHOW FULL COLUMNS FROM t1 where `Key` = 'PRI';
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
| Field | Type    | Collation | Null | Key | Default | Extra | Privileges                      | Comment |
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
| A     | int(11) | NULL      | NO   | PRI | NULL    |       | select,insert,update,references |         |
+-------+---------+-----------+------+-----+---------+-------+---------------------------------+---------+
1 row in set (0.04 sec)
```

### SHOW CREATE TABLE

`Syntax`
```
SHOW CREATE TABLE table_name
```

`Instructions`
* N/A

`Example: `
```
mysql> SHOW CREATE TABLE t1\G
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `id` int(11) DEFAULT NULL,
  `age` int(11) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
/*!50100 PARTITION BY HASH (id) */
1 row in set (0.094 sec)
```

### SHOW PROCESSLIST

`Syntax`
```
SHOW PROCESSLIST
```

`Instructions`
* Shows the connection from client to RadonDB, not the backend partition MySQL

`Example: `
```
mysql> SHOW PROCESSLIST;
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
| Id   | User | Host            | db       | Command | Time | State | Info | Rows_sent | Rows_examined |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
|    1 | root | 127.0.0.1:56984 | db_test1 | Sleep   |  794 |       |      |         0 |             0 |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
1 row in set (0.00 sec)
```

### SHOW VARIABLES

`Syntax`
```
SHOW VARIABLES
    [LIKE 'pattern' | WHERE expr]
```

`Instructions`
* For compatibility JDBC/mydumper
* The SHOW VARIABLES command is sent to the backend partition MySQL (random partition) to get and return

## KILL

### KILL processlist_id

`Syntax`
```
KILL processlist_id
```

`Instructions`
* Kill a link (including terminating the executing statement)

`Example: `

```
mysql> show processlist;
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
| Id   | User | Host            | db       | Command | Time | State | Info | Rows_sent | Rows_examined |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
|    2 | root | 127.0.0.1:38382 | db_test1 | Sleep   |  197 |       |      |         0 |             0 |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
1 row in set (0.00 sec)

mysql> kill 2;
ERROR 2013 (HY000): Lost connection to MySQL server during query

mysql> show processlist;
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect...
Connection id:    3
Current database: db_test1

+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
| Id   | User | Host            | db       | Command | Time | State | Info | Rows_sent | Rows_examined |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
|    3 | root | 127.0.0.1:38516 | db_test1 | Sleep   |    0 |       |      |         0 |             0 |
+------+------+-----------------+----------+---------+------+-------+------+-----------+---------------+
1 row in set (0.00 sec)

```

## USE

### USE DATABASE

`Syntax`
```
USE db_name
```

`Instructions`
* Switch the database of the current session

`Example: `
```
mysql> use db_test1;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```
