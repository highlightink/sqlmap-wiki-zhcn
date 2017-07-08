## 常用选项

下面的选项可用于设置 sqlmap 的常规参数。

### 从存储(.sqlite)文件读取会话 

选项：`-s`

sqlmap 会分别在结果目录自动为每一个目标建立持久会话 SQLite 文件，该文件会存储用于会话恢复的所有数据。如果用户需要指定会话文件存储地址(例如将所有目标会话数据存储在同一个位置), 则可以使用这个选项。

### 保存 HTTP(s) 访问信息日志到文本文件

选项: `-t`

这个选项提供指定一个文件地址参数，用于 sqlmap 产生的所有HTTP(s) 流量信息写入 -- 包括 HTTP(S) 请求 和 HTTP(S) 响应。

这个选项主要作用是用于调试 - 当你向开发人员提供潜在 bug 报告时，可把这个文件一并带上。 

### 以非交互式模式运行

开关: `--batch`

当你需要以批处理模式运行 sqlmap，避免任何用户干预 sqlmap 的运行，你可以强制使用 `--batch` 这个开关。 这样，当 sqlmap 需要用户输入信息时， 都将会以默认参数运行。

### 二进制内容检索

选项：`--binary-fields`

为了便于对表单可能存在二进制格式数据列(例如：数据列 `password` 存储了密码哈希值二进制数据) 进行二进制检索，可使用`--binary-fields` 选项对该数据列进行合适的处理。 所有的这些数据域都将被取出并以十六进制格式展示，便于被后续其他工具处理。(例如：`john`)

### 强制指定数据检索编码

选项: `--charset`

为了对字符数据进行合理的编码， sqlmap 使用从 web 服务器提供的信息(例如：HTTP请求头 `Content-Type`), 或者使用第三方库[chardet](https://pypi.python.org/pypi/chardet)进行推导。 

尽管如此，有时候还是需要对编码值进行指定，特别是当获取的数据包含国际化的非 ASCII 字符时(例如：`charset=GBK`)。同时，需要注意的是，如果目标机器存储数据格式与数据库连接器编码不兼容，则会不可避免地出现编码信息丢失的情况。

### 从目标 URL 开始抓取站点

选项: `--crawl`

sqlmap 可以从给定的目标站点开始收集(抓取)存在潜在漏洞的链接。默认 sqlmap 会递归抓取所有新的链接，使用这个选项可以设置链接的深度(到起始链接的距离), 这样 sqlmap 处理到对应深度就不会继续处理。

以 MySQL 目标进行举例:

```
$ python sqlmap.py -u "http://192.168.21.128/sqlmap/mysql/" --batch --crawl=3
[...]
[xx:xx:53] [INFO] starting crawler
[xx:xx:53] [INFO] searching for links with depth 1
[xx:xx:53] [WARNING] running in a single-thread mode. This could take a while
[xx:xx:53] [INFO] searching for links with depth 2
[xx:xx:54] [INFO] heuristics detected web page charset 'ascii'
[xx:xx:00] [INFO] 42/56 links visited (75%)
[...]
```

选项 `--crawl-exclude`

使用这个选项你可以通过提供正则表达式用于排除要抓取的页面。例如，如果你想要跳过所有包含`logout` 关键字的链接，你可以使用`--crawl-exclude=logout`. 

### 指定 CSV 输出的分隔符

选项: `--csv-del`

当导出数据到 CSV 格式文件(`--dump-format=CSV`), 数据列需要使用“分隔值”进行划分。 如果用户想要覆盖默认分隔符，则可应使用这个选项(例如：`--cvs-del=";"`)。

### DBMS  授权凭证

选项: `--dbms-cred`

某些情况下，用户可能会因为当前数据库管理系统用户的权限问题而导致操作失败，这时就可以使用这个选项。
In some cases user will be warned that some operations failed because of lack of current DBMS user privileges and that he could try to use this option. In those cases, if he provides `admin` user credentials to sqlmap by using this option, sqlmap will try to rerun the problematic part with specialized "run as" mechanisms (e.g. `OPENROWSET` on Microsoft SQL Server) using those credentials.

### Format of dumped data

Option: `--dump-format`

sqlmap supports three different types of formatting when storing dumped table data into the corresponding file inside an output directory: `CSV`, `HTML` and `SQLITE`. Default one is `CSV`, where each table row is stored into a textual file line by line, and where each entry is separated with a comma character `,` (or one provided with option `--csv-del`). In case of `HTML`, output is being stored into a HTML file, where each row is represented with a row inside a formatted table. In case of `SQLITE`, output is being stored into a SQLITE database, where original table content is replicated into the corresponding table having a same name.

### Estimated time of arrival

Switch: `--eta`

It is possible to calculate and show in real time the estimated time of arrival to retrieve each query output. This is shown when the technique used to retrieve the output is any of the blind SQL injection types. 

Example against an Oracle target affected only by boolean-based blind SQL injection:

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/oracle/get_int_bool.php?id\
=1" -b --eta

[...]
[hh:mm:01] [INFO] the back-end DBMS is Oracle
[hh:mm:01] [INFO] fetching banner
[hh:mm:01] [INFO] retrieving the length of query output
[hh:mm:01] [INFO] retrieved: 64
17% [========>                                          ] 11/64  ETA 00:19
```

Then:

```
100% [===================================================] 64/64
[hh:mm:53] [INFO] retrieved: Oracle Database 10g Enterprise Edition Release 10.2
.0.1.0 - Prod

web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: Oracle
banner:    'Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Prod'
```

As you can see, sqlmap first calculates the length of the query output, then estimates the time of arrival, shows the progress in percentage and counts the number of retrieved output characters. 

### Flush session files

Option: `--flush-session`

As you are already familiar with the concept of a session file from the description above, it is good to know that you can flush the content of that file using option `--flush-session`. This way you can avoid the caching mechanisms implemented by default in sqlmap. Other possible way is to manually remove the session file(s). 

### Parse and test forms' input fields

Switch: `--forms`

Say that you want to test against SQL injections a huge _search form_ or you want to test a login bypass (typically only two input fields named like _username_ and _password_), you can either pass to sqlmap the request in a request file (`-r`), set the POSTed data accordingly (`--data`) or let sqlmap do it for you! 

Both of the above mentioned instances, and many others, appear as ` <form>` and ` <input>` tags in HTML response bodies and this is where this switch comes into play.

Provide sqlmap with `--forms` as well as the page where the form can be found as the target URL (`-u`) and sqlmap will request the target URL for you, parse the forms it has and guide you through to test for SQL injection on those form input fields (parameters) rather than the target URL provided. 

### Ignore query results stored in session file

Switch: `--fresh-queries`

As you are already familiar with the concept of a session file from the description above, it is good to know that you can ignore the content of that file using option `--fresh-queries`. This way you can keep the session file untouched and for a selected run, avoid the resuming/restoring of queries output. 

### Use DBMS hex function(s) for data retrieval

Switch: `--hex`

In lost of cases retrieval of non-ASCII data requires special needs. One solution for that problem is usage of DBMS hex function(s). Turned on by this switch, data is encoded to it's hexadecimal form before being retrieved and afterwards unencoded to it's original form.

Example against a PostgreSQL target:

```
$ python sqlmap.py -u "http://192.168.48.130/sqlmap/pgsql/get_int.php?id=1" --b\
anner --hex -v 3 --parse-errors

[...]
[xx:xx:14] [INFO] fetching banner
[xx:xx:14] [PAYLOAD] 1 AND 5849=CAST((CHR(58)||CHR(118)||CHR(116)||CHR(106)||CHR
(58))||(ENCODE(CONVERT_TO((COALESCE(CAST(VERSION() AS CHARACTER(10000)),(CHR(32)
))),(CHR(85)||CHR(84)||CHR(70)||CHR(56))),(CHR(72)||CHR(69)||CHR(88))))::text||(
CHR(58)||CHR(110)||CHR(120)||CHR(98)||CHR(58)) AS NUMERIC)
[xx:xx:15] [INFO] parsed error message: 'pg_query() [<a href='function.pg-query'
>function.pg-query</a>]: Query failed: ERROR:  invalid input syntax for type num
eric: ":vtj:506f737467726553514c20382e332e39206f6e20693438362d70632d6c696e75782d
676e752c20636f6d70696c656420627920474343206763632d342e332e7265616c20284465626961
6e2032e332e322d312e312920342e332e32:nxb:" in <b>/var/www/sqlmap/libs/pgsql.inc.p
hp</b> on line <b>35</b>'
[xx:xx:15] [INFO] retrieved: PostgreSQL 8.3.9 on i486-pc-linux-gnu, compiled by 
GCC gcc-4.3.real (Debian 4.3.2-1.1) 4.3.2
[...]
```

### Custom output directory path

Option: `--output-dir`

sqlmap by default stores session and result files inside a subdirectory `output`. In case you want to use a different location, you can use this option (e.g. `--output-dir=/tmp`).

### Parse DBMS error messages from response pages

Switch: `--parse-errors`

If the web application is configured in debug mode so that it displays in the HTTP responses the back-end database management system error messages, sqlmap can parse and display them for you.

This is useful for debugging purposes like understanding why a certain enumeration or takeover switch does not work - it might be a matter of session user's privileges and in this case you would see a DBMS error message along the lines of `Access denied for user  <SESSION USER>`. 

Example against a Microsoft SQL Server target:

```
$ python sqlmap.py -u "http://192.168.21.129/sqlmap/mssql/iis/get_int.asp?id=1"\
 --parse-errors
[...]
[xx:xx:17] [INFO] ORDER BY technique seems to be usable. This should reduce the 
timeneeded to find the right number of query columns. Automatically extending th
e rangefor current UNION query injection technique test
[xx:xx:17] [INFO] parsed error message: 'Microsoft OLE DB Provider for ODBC Driv
ers (0x80040E14)
[Microsoft][ODBC SQL Server Driver][SQL Server]The ORDER BY position number 10 i
s out of range of the number of items in the select list.
<b>/sqlmap/mssql/iis/get_int.asp, line 27</b>'
[xx:xx:17] [INFO] parsed error message: 'Microsoft OLE DB Provider for ODBC Driv
ers (0x80040E14)
[Microsoft][ODBC SQL Server Driver][SQL Server]The ORDER BY position number 6 is
 out of range of the number of items in the select list.
<b>/sqlmap/mssql/iis/get_int.asp, line 27</b>'
[xx:xx:17] [INFO] parsed error message: 'Microsoft OLE DB Provider for ODBC Driv
ers (0x80040E14)
[Microsoft][ODBC SQL Server Driver][SQL Server]The ORDER BY position number 4 is
 out of range of the number of items in the select list.
<b>/sqlmap/mssql/iis/get_int.asp, line 27</b>'
[xx:xx:17] [INFO] target URL appears to have 3 columns in query
[...]
```

### Save options in a configuration INI file

Option: `--save`

It is possible to save the command line options to a configuration INI file. The generated file can then be edited and passed to sqlmap with the `-c` option as explained above.

### Update sqlmap

Switch: `--update`

Using this option you can update the tool to the latest development version directly from the [Git repository](https://github.com/sqlmapproject/sqlmap.git). You obviously need Internet access. 

If, for any reason, this operation fails, run `git pull` from your sqlmap working copy. It will perform the exact same operation of switch `--update`. If you are running sqlmap on Windows, you can use the [SmartGit](http://www.syntevo.com/smartgit/index.html) client. 

This is strongly recommended **before** reporting any bug to the [mailing lists](http://www.sqlmap.org/#ml).
