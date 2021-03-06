---
title: CREATE USER | TiDB SQL Statement Reference
summary: An overview of the usage of CREATE USER for the TiDB database.
aliases: ['/docs/v3.1/sql-statements/sql-statement-create-user/','/docs/v3.1/reference/sql/statements/create-user/']
---

# CREATE USER

This statement creates a new user, specified with a password. In the MySQL privilege system, a user is the combination of a username and the host from which they are connecting from. Thus, it is possible to create a user `'newuser2'@'192.168.1.1'` who is only able to connect from the IP address `192.168.1.1`. It is also possible to have two users have the same user-portion, and different permissions as they login from different hosts.

## Synopsis

**CreateUserStmt:**

![CreateUserStmt](https://download.pingcap.com/images/docs/sqlgram/CreateUserStmt.png)

**IfNotExists:**

![IfNotExists](https://download.pingcap.com/images/docs/sqlgram/IfNotExists.png)

**UserSpecList:**

![UserSpecList](https://download.pingcap.com/images/docs/sqlgram/UserSpecList.png)

**UserSpec:**

![UserSpec](https://download.pingcap.com/images/docs/sqlgram/UserSpec.png)

**AuthOption:**

![AuthOption](https://download.pingcap.com/images/docs/sqlgram/AuthOption.png)

**StringName:**

![StringName](https://download.pingcap.com/images/docs/sqlgram/StringName.png)

## Examples

```sql
mysql> CREATE USER 'newuser' IDENTIFIED BY 'newuserpassword';
Query OK, 1 row affected (0.04 sec)

mysql> CREATE USER 'newuser2'@'192.168.1.1' IDENTIFIED BY 'newuserpassword';
Query OK, 1 row affected (0.02 sec)
```

## MySQL compatibility

* Several of the `CREATE` options are not yet supported by TiDB, and will be parsed but ignored.

## See also

* [Security Compatibility with MySQL](/security-compatibility-with-mysql.md)
* [DROP USER](/sql-statements/sql-statement-drop-user.md)
* [SHOW CREATE USER](/sql-statements/sql-statement-show-create-user.md)
* [ALTER USER](/sql-statements/sql-statement-alter-user.md)
* [Privilege Management](/privilege-management.md)
