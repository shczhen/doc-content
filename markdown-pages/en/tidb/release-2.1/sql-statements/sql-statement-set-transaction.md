---
title: SET TRANSACTION | TiDB SQL Statement Reference
summary: An overview of the usage of SET TRANSACTION for the TiDB database.
aliases: ['/docs/v2.1/sql-statements/sql-statement-set-transaction/','/docs/v2.1/reference/sql/statements/set-transaction/']
---

# SET TRANSACTION

The `SET TRANSACTION` statement can be used to change the current isolation level on a `GLOBAL` or `SESSION` basis. This syntax is an alternative to `SET transaction_isolation='new-value'` and is included for compatibility with both MySQL, and the SQL standards.

## Synopsis

**SetStmt:**

![SetStmt](https://download.pingcap.com/images/docs/sqlgram/SetStmt.png)

**TransactionChar:**

![TransactionChar](https://download.pingcap.com/images/docs/sqlgram/TransactionChar.png)

**IsolationLevel:**

![IsolationLevel](https://download.pingcap.com/images/docs/sqlgram/IsolationLevel.png)

## Examples

```sql
mysql> SHOW SESSION VARIABLES like 'transaction_isolation';
+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.00 sec)

mysql> SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW SESSION VARIABLES like 'transaction_isolation';
+-----------------------+----------------+
| Variable_name         | Value          |
+-----------------------+----------------+
| transaction_isolation | READ-COMMITTED |
+-----------------------+----------------+
1 row in set (0.01 sec)

mysql> SET SESSION transaction_isolation = 'REPEATABLE-READ';
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW SESSION VARIABLES like 'transaction_isolation';
+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.00 sec)
```

## MySQL compatibility

* TiDB supports the ability to set a transaction as read-only in syntax only.
* The isolation levels `READ-UNCOMMITTED` and `SERIALIZABLE` are not supported.
* The isolation level `REPEATABLE-READ` is technically Snapshot Isolation. The name `REPEATABLE-READ` is used for compatibility with MySQL.

## See also

* [`SET [GLOBAL|SESSION] <variable>`](/sql-statements/sql-statement-set-variable.md)
* [Isolation Levels](/transaction-isolation-levels.md)
