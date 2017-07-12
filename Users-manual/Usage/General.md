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

某些情况下，用户可能会因为当前数据库管理系统用户的权限问题而导致操作失败，这时就可以使用这个选项。在这种情景下，如果用户使用这个选项用于提供`admin` 用户凭证， sqlmap 则会尝试使用相应的凭证信息，使用`以其他身份运行`机制(例如：Microsoft SQL Server `OPENROWSET`)。

### 导出数据格式

选项: `--dump-format`

当导出数据表数据到输出目录的对应文件时， sqlmap 支持三种不同数据导出格式：`CSV`, `HTML`, `SQLITE`.默认的输出格式是 `CSV`，每一条数据以逗号(或者使用`--csv-del`指定其他)为分隔符，逐行存储到文本文件中。如果是使用`HTML`格式，则输出会被存储到 HTML 文件，每行数据会被以表格一行存储到 HTML 文件中。如果是使用`SQLITE`，数据则会被存储到 SQLITE 数据库，原先的数据表则会被转换成具有相同名字的 SQLITE 数据表。

sqlmap supports three different types of formatting when storing dumped table data into the corresponding file inside an output directory: `CSV`, `HTML` and `SQLITE`. Default one is `CSV`, where each table row is stored into a textual file line by line, and where each entry is separated with a comma character `,` (or one provided with option `--csv-del`). In case of `HTML`, output is being stored into a HTML file, where each row is represented with a row inside a formatted table. In case of `SQLITE`, output is being stored into a SQLITE database, where original table content is replicated into the corresponding table having a same name.

### 预估完成时间

开关: `--eta`

sqlmap 支持实时计算并显示获取查询结果的预估时间。如果使用的技术是任意一种 SQL 盲注，则会显示输出获取时间。

针对 Oracle 目标实施布尔型盲注的例子：

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

从上面可以看出，sqlmap 会先计算出查询结果的长度，然后预估完成的时间，并显示出完成的百分比及接收到的字符数。

### 清空会话文件

选项: `--flush-session`

通过上面的相关描述，相信你已经熟悉了会话文件的相关概念，值得注意的是，你可以通过选项 `--flush-session` 清空会话文件内容。这样你可以避免 sqlmap 默认的缓存机制。你也可以通过手动移除相关的会话文件。

### 解析并测试输入表单输入域

开关: `--forms`

例如你需要针对搜索框进行 SQL 注入测试或者你想绕过登录验证(通常是用户名和密码两个输入框)，你可以通过给 sqlmap 传入请求文件(`-r`)，并设置好相关的提交数据(`--data`), 或者直接让 sqlmap 自动为你完成相关操作。

上面提及的两个实例，及其他 HTML 响应内容中出现 `<form>` 和 `<input>` 标签，都可以使用这个开关。

配合存在相关表单的目标 URL (`-u`) 使用 sqlmap `--forms` 开关，sqlmap 会自动为你请求对应目标 URL，解析相关的表单，并引导你通过基于表单输入域进行相关的 SQL 注入测试。

### 忽略会话文件中的查询结果

开关: `--fresh-queries`

通过上面的相关描述，相信你已经熟悉了会话文件的相关概念，值得注意的是，你可以使用`--fresh-queries` 这个开关忽略指定的会话文件。这样的话，你可以保持特定的会话文件内容不被修改，并且对于指定测试，避免了保存/回复查询结果。

### 使用 DBMS hex 函数进行数据检索

开关: `--hex`

大多数情况下，检索 non-ASCII 数据都会有特殊要求。其中一个解决方案就是使用 DBMS hex 函数。开启这个开关，则数据在被获取之前，会被编码成十六进制格式，并在随后被解码成原先的格式。

针对 PostgreSQL 目标的例子:

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

### 指定输出文件路径

选项: `--output-dir`

默认，sqlmap 会将会话和结果文件存储在命名为 `output`的子文件目录。如果你想要使用不同的存储位置，你可以使用这个选项。(例如：`--output-dir=/tmp`)

### 解析响应请求返回的 DBMS 错误信息

开关: `--parse-errors`

如果 web 应用配置了调试模式，相关后端数据库管理系统的错误信息会在 HTTP 响应请求中显示，则 sqlmap 会对其进行解析并展示。

这个特性可用于相关调试，类似理解为什么特定枚举或者入侵开关失效，例如会话用户出现权限问题，你可以看到`拒绝<会话用户>访问` 的 DBMS 错误信息。

针对 Microsoft SQL Server 目标的例子：


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

### 保存相关选项到 INI 配置文件

选项: `--save`

我们可以将命令行上的相关参数保存到 sqlmap INI 配置文件中。 同时可以对生成的文件进行编辑，或者通过上面描述的 `-c` 选项进行使用。

### 更新 sqlmap

开关: `--update`

使用这个开关，你可以直接从 [Git repository](https://github.com/sqlmapproject/sqlmap.git) 上将该工具升级到最新的开发版本。当然，你需要网络连接。

当然，如果上面的操作失败，你可以直接在 sqlmap 所在目录运行`git pull`。这样的执行效果和使用开关`--update`一样。如果你是在 Windows 上使用 sqlmap，你可以使用[SmartGit](http://www.syntevo.com/smartgit/index.html) 客户端。

在向[mailing lists](http://www.sqlmap.org/#ml) 反馈任何潜在 bug 之前，强烈建议先行尝试上面描述的方法。
