---
title: The System Variables
summary: Learn how to use the system variables in TiDB.
aliases: ['/docs/v3.0/system-variables/','/docs/v3.0/reference/configuration/tidb-server/mysql-variables/','/docs/sql/variable/']
---

# The System Variables

The system variables in MySQL are the system parameters that modify the operation of the database runtime. These variables have two types of scope, Global Scope and Session Scope. TiDB supports all the system variables in MySQL 5.7. Most of the variables are only supported for compatibility and do not affect the runtime behaviors.

## Set the system variables

You can use the [`SET`](/sql-statements/sql-statement-set-variable.md) statement to change the value of the system variables. Before you change, consider the scope of the variable. For more information, see [MySQL Dynamic System Variables](https://dev.mysql.com/doc/refman/5.7/en/dynamic-system-variables.html).

### Set Global variables

Add the `GLOBAL` keyword before the variable or use `@@global.` as the modifier:

```sql
SET GLOBAL autocommit = 1;
SET @@global.autocommit = 1;
```

> **Note:**
>
> In a distributed TiDB database, a variable's `GLOBAL` setting is persisted to the storage layer. A single TiDB instance proactively gets the `GLOBAL` information and forms `gvc` (global variables cache) every 2 seconds. The cache information remains valid within 2 seconds. When you set the `GLOBAL` variable, to ensure the effectiveness of new sessions, make sure that the interval between two operations is larger than 2 seconds. For details, see [Issue #14531](https://github.com/pingcap/tidb/issues/14531).

### Set Session variables

Add the `SESSION` keyword before the variable, use `@@session.` as the modifier, or use no modifier:

```sql
SET SESSION autocommit = 1;
SET @@session.autocommit = 1;
SET @@autocommit = 1;
```

> **Note:**
>
> `LOCAL` and `@@local.` are the synonyms for `SESSION` and `@@session.`

### The working mechanism of system variables

* Session variables will only initialize their own values based on global variables when a session is created. Changing a global variable does not change the value of the system variable being used by the session that has already been created.

    ```sql
    mysql> SELECT @@GLOBAL.autocommit;
    +---------------------+
    | @@GLOBAL.autocommit |
    +---------------------+
    | ON                  |
    +---------------------+
    1 row in set (0.00 sec)

    mysql> SELECT @@SESSION.autocommit;
    +----------------------+
    | @@SESSION.autocommit |
    +----------------------+
    | ON                   |
    +----------------------+
    1 row in set (0.00 sec)

    mysql> SET GLOBAL autocommit = OFF;
    Query OK, 0 rows affected (0.01 sec)

    mysql> SELECT @@SESSION.autocommit; -- Session variables do not change, and the transactions in the session are executed in the form of autocommit.
    +----------------------+
    | @@SESSION.autocommit |
    +----------------------+
    | ON                   |
    +----------------------+
    1 row in set (0.00 sec)

    mysql> SELECT @@GLOBAL.autocommit;
    +---------------------+
    | @@GLOBAL.autocommit |
    +---------------------+
    | OFF                 |
    +---------------------+
    1 row in set (0.00 sec)

    mysql> exit
    Bye
    $ mysql -h127.0.0.1 -P4000 -uroot -D test
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 3
    Server version: 5.7.25-TiDB-None MySQL Community Server (Apache License 2.0)

    Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql> SELECT @@SESSION.autocommit; -- The newly created session uses a new global variable.
    +----------------------+
    | @@SESSION.autocommit |
    +----------------------+
    | OFF                  |
    +----------------------+
    1 row in set (0.00 sec)
    ```

## The fully supported MySQL system variables in TiDB

The following MySQL system variables are fully supported in TiDB and have the same behaviors as in MySQL.

| Name | Scope | Description |
| ---------------- | -------- | -------------------------------------------------- |
| autocommit | GLOBAL \| SESSION | whether automatically commit a transaction|
| sql_mode | GLOBAL \| SESSION | support some of the MySQL SQL modes|
| time_zone | GLOBAL \| SESSION | the time zone of the database |
| tx_isolation | GLOBAL \| SESSION | the isolation level of a transaction |
| max\_execution\_time | GLOBAL \| SESSION | the execution timeout for a statement, in milliseconds |
| innodb\_lock\_wait\_timeout | GLOBAL \| SESSION | the lock wait time for pessimistic transactions, in seconds |
| interactive\_timeout | SESSION \| GLOBAL | the idle timeout of the interactive user session, in seconds   |

> **Note:**
>
> Unlike in MySQL, the `max_execution_time` system variable currently works on all kinds of statements in TiDB, not only restricted to the `SELECT` statement. The precision of the timeout value is roughly 100ms. This means the statement might not be terminated in accurate milliseconds as you specify.

## TiDB specific system variables

See [TiDB Specific System Variables](/tidb-specific-system-variables.md).
