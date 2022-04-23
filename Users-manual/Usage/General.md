# 常规选项

> *译自：[General](https://github.com/sqlmapproject/sqlmap/wiki/Usage#general)*

下面的选项用于设置 sqlmap 的常规参数。

## 从已存储（.sqlite）文件读取会话

选项：`-s`

sqlmap 会在专用的输出目录中自动为每一个目标分别建立持久会话 SQLite 文件，该文件会存储用于恢复会话的所有数据。如果用户需要指定会话文件的具体存储位置（例如：将所有目标的会话数据存储在同一个位置），则可以使用这个选项。

## 记录 HTTP(s) 访问信息到文本文件

选项：`-t`

这个选项需要一个指定文本文件地址的参数，用于写入 sqlmap 产生的所有 HTTP(s) 流量信息——包括 HTTP(S) 请求 和 HTTP(S) 响应。

这个选项主要用于调试——当你向开发人员提供潜在 bug 报告时，可把这个文件一并带上。

## 为问题预设答案

选项：`--answers`

如果用户想要自动回答问题，即使使用了 `--batch` 选项，也可以通过在等号后提供一部分的问题和对应的回答来做到这一点。另外，不同问题的答案可以用分隔符 `,` 分割。

针对 MySQL 目标的示例：

```sh
$ python sqlmap.py -u "http://192.168.22.128/sqlmap/mysql/get_int.php?id=1"--te\
chnique=E --answers="extending=N" --batch
[...]
[xx:xx:56] [INFO] testing for SQL injection on GET parameter 'id'
heuristic (parsing) test showed that the back-end DBMS could be 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
[xx:xx:56] [INFO] do you want to include all tests for 'MySQL' extending provide
d level (1) and risk (1)? [Y/n] N
[...]
```

### 声明（表明）参数中包含了 Base64 编码的数据

选项：`--base64`

在目标 Web 应用使用 Base64 编码来存储特定参数数据时（例如：用 Base64 来编码 JSON 字典），用户可以使用选项 `--base64` 声明，使 sqlmap 能正确地使用参数值进行测试。

使用例子：（注意：`Base64('{"id": 1}') == 'eyJpZCI6IDF9'`）：

```sh
$ python sqlmap.py -u http://192.168.22.128/sqlmap/mysql/get_base64?value=eyJpZC\
I6IDF9 -v 5 --base64=value
[...]
[23:43:35] [INFO] testing 'Boolean-based blind - Parameter replace (original valu
e)'
[23:43:35] [PAYLOAD] KFNFTEVDVCAoQ0FTRSBXSEVOICgzODY1PTUzMTQpIFRIRU4gJ3siaWQiOiAx
fScgRUxTRSAoU0VMRUNUIDUzMTQgVU5JT04gU0VMRUNUIDE5MzIpIEVORCkp
[23:43:35] [TRAFFIC OUT] HTTP request [#11]:
GET /?value=KFNFTEVDVCAoQ0FTRSBXSEVOICgzODY1PTUzMTQpIFRIRU4gJ3siaWQiOiAxfScgRUxTR
SAoU0VMRUNUIDUzMTQgVU5JT04gU0VMRUNUIDE5MzIpIEVORCkp HTTP/1.1
Host: localhost
Cache-control: no-cache
Accept-encoding: gzip,deflate
Accept: */*
User-agent: sqlmap/1.4.4.3#dev (http://sqlmap.org)
Connection: close
[...]
```

## 以非交互式模式运行

开关：`--batch`

当你需要以批处理模式运行 sqlmap，避免任何用户干预 sqlmap 的运行，可以强制使用 `--batch` 这个开关。这样，当 sqlmap 需要用户输入信息时，都将会以默认参数运行。

## 二进制内容检索

选项：`--binary-fields`

为了便于检索存储二进制数值（例如：数据列 `password` 存储了密码哈希值二进制数据）的数据表字段的内容，可使用 `--binary-fields` 选项对该数据列进行（额外）适当处理。所有的这些数据域（例如：数据表的列）都将被取出并以十六进制格式展示，便于后续被其他工具（例如：`john`）处理。

## 自定义 SQL（盲）注入字符集

选项：`--charset`

在布尔型盲注（Boolean-based blind）和时间型盲注（Time-based blind）中，用户可以强制使用自定义字符集来加快数据检索过程。例如，在导出消息摘要值（例如：SHA1）时，通过使用（例如）`--charset="0123456789abcdef"`，预期的请求数比常规运行大约少 30%。

## 从目标 URL 开始爬取站点

选项：`--crawl`

sqlmap 可以从给定的目标站点开始收集（抓取）存在潜在漏洞的链接。使用这个选项可以设置爬取的深度（到起始位置的距离），这样 sqlmap 爬取到对应深度就不会继续进行。

针对 MySQL 目标的运行示例：

```shell
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

选项：`--crawl-exclude`

使用这个选项可以通过正则表达式来排除不想抓取的页面。例如，如果你想要跳过所有包含 `logout` 关键字的链接，你可以使用 `--crawl-exclude=logout`。

## 指定 CSV 输出的分隔符

选项：`--csv-del`

当导出数据到 CSV 格式文件（`--dump-format=CSV`），数据条目需要使用“分隔符”（默认为 `,`）进行划分。如果用户想要覆盖默认分隔符，可以使用这个选项（例如：`--csv-del=";"`)。

## DBMS（Database Management System，数据库管理系统）认证凭证

选项：`--dbms-cred`

某些情况下，用户可能会因为当前 DBMS 用户的权限问题而导致操作失败，这时就可以使用这个选项。在这种情景下，如果用户使用这个选项来 `admin` 用户凭证，sqlmap 会尝试使用相应的认证信息与“以其他身份运行”机制（例如：Microsoft SQL Server 的 `OPENROWSET`）来重新运行。

## 导出数据的格式

选项：`--dump-format`

当导出数据表数据到输出目录的相应文件时，sqlmap 支持三种不同的数据导出格式：`CSV`，`HTML` 和 `SQLITE`。默认的输出格式是 `CSV`，每一条数据以 `,`（或者使用 `--csv-del` 指定其他符号）为分隔符逐行存储到文本文件中。如果是使用 `HTML` 格式，则输出会被存储为 HTML 文件，每行数据会被存储为表格的一行到 HTML 文件中。如果是使用 `SQLITE`，数据则会被存储到 SQLITE 数据库，原先的数据表会被转换成具有相同名字的 SQLITE 数据表。

## 强制指定检索数据编码

选项：`--encoding`

为了对字符数据进行合理的编码，sqlmap 使用从 Web 服务器提供的信息（例如：HTTP 请求头 `Content-Type`），或是使用第三方库 [chardet](https://pypi.python.org/pypi/chardet) 进行推导。

尽管如此，有时候还是需要对编码进行指定，特别是当获取的数据包含国际化的非 ASCII 字符时（例如：`encoding=GBK`）。同时，需要注意的是，如果目标机器数据库存储的数据内容与数据库连接器编码不兼容，则会不可逆转地出现编码信息丢失的情况。

## 预估完成时间

开关：`--eta`

sqlmap 支持实时计算并显示获取查询结果的预估时间。如果使用的技术是任意一种 SQL 盲注，则会显示获取输出的时间。

针对 Oracle 目标进行布尔型盲注的例子：

```shell
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/oracle/get_int_bool.php?id\
=1" -b --eta

[...]
[hh:mm:01] [INFO] the back-end DBMS is Oracle
[hh:mm:01] [INFO] fetching banner
[hh:mm:01] [INFO] retrieving the length of query output
[hh:mm:01] [INFO] retrieved: 64
17% [========>                                          ] 11/64  ETA 00:19
```

接着：

```shell
100% [===================================================] 64/64
[hh:mm:53] [INFO] retrieved: Oracle Database 10g Enterprise Edition Release 10.2
.0.1.0 - Prod

web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: Oracle
banner:    'Oracle Database 10g Enterprise Edition Release 10.2.0.1.0 - Prod'
```

从上面可以看出，sqlmap 会先计算出查询结果的长度，然后预估完成的时间，并显示出完成的百分比及接收到的字符数。

## 清空会话文件

选项：`--flush-session`

经过上面的相关描述，相信你已经熟悉了会话文件的相关概念，值得注意的是，你可以通过选项 `--flush-session` 来清空会话文件内容。这样你可以避免 sqlmap 默认的缓存机制。也可以手动移除相关的会话文件。

## 解析并测试表单输入域

开关：`--forms`

例如你需要针对_搜索框_进行 SQL 注入测试，或者你想绕过登录验证（通常是 *username* 和 *password* 两个输入框），你可以通过给 sqlmap 传入请求文件（`-r`），并设置好（`--data`）相关的提交数据，或者直接让 sqlmap 自动为你完成相关操作。

上面提及的两个实例，及其他 HTML 响应体中出现的 `<form>` 和 `<input>` 标签，都可以使用这个开关。

配合存在表单的目标 URL（`-u`）使用 sqlmap 的 `--forms` 开关，sqlmap 会自动为你请求对应目标 URL，解析相关的表单，并引导你基于表单输入域（参数）而非提供的目标 URL 进行 SQL 注入测试。

## 忽略会话文件中的查询结果

开关：`--fresh-queries`

经过上面的描述，相信你已经熟悉了会话文件的概念，值得注意的是，你可以使用 `--fresh-queries`
这个开关忽略会话文件中的查询结果。这样你就可以保持某次运行的特定会话文件内容不被修改，
从而避免重复尝试及恢复查询结果。

## 使用 DBMS hex 函数获取数据

开关：`--hex`

很多情况下，获取非 ASCII 数据都会有特殊要求。其中一个解决方案就是使用 DBMS hex 函数。开启这个开关，数据在被获取之前，会被编码成十六进制格式，并在随后被解码成原先的格式。

针对 PostgreSQL 目标的例子：

```shell
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
[xx:xx:15] [INFO] retrieved: PostgreSQL 8.3.9 on i486-pc-linux-gnu, compiled by GCC gcc-4.3.real (Debian 4.3.2-1.1) 4.3.2
[...]
```

## 指定输出目录路径

选项：`--output-dir`

默认情况下，sqlmap 会将会话和结果文件存储在命名为 `output` 的子目录中。如果你想要使用不同的存储位置，可以用这个选项（例如：`--output-dir=/tmp`）。

## 从响应页面中解析 DBMS 错误信息

开关：`--parse-errors`

如果 Web 应用配置了调试模式，后端 DBMS 的错误信息会在 HTTP 响应请求中显示，sqlmap 会对其进行解析并展示。

这个特性对调试非常有用，例如可以用来理解为什么特定枚举或接管开关会失效——可能是会话用户出现权限问题，在这种情况下，你可以看到 `Access denied for user  <SESSION USER>（拒绝用户<会话用户>访问）`的 DBMS 错误信息。

针对 Microsoft SQL Server 目标的例子：

```shell
$ python sqlmap.py -u "http://192.168.21.129/sqlmap/mssql/iis/get_int.asp?id=1"\
 --parse-errors
[...]
[xx:xx:17] [INFO] ORDER BY technique seems to be usable. This should reduce the timeneeded to find the right number of query columns. Automatically extending th
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

## 前处理（请求）

选项：`--preprocess`

使用此选项，可在（HTTP）请求数据被发送之前先由给定的前处理脚本处理（如微调请求内容）。例如，追加 query 参数到 POST 请求体的前处理脚本如下：

```py
#!/usr/bin/env python

def preprocess(req):
    if req.data:
        req.data += b'&foo=bar'
```

### 后处理（响应）

响应：`--postprocess`

使用此选项，可在 sqlmap 检测引擎之前先由后处理脚本处理（HTTP）响应数据（如解码数据或删除无用数据）。例如，将所有小写字符转换为大写的后处理脚本如下：

```py
#!/usr/bin/env python

def postprocess(page, headers=None, code=None):
    return page.upper() if page else page, headers, code
```

## 保存相关选项到 INI 配置文件

选项：`--save`

使用此开关可以将命令行上的相关选项保存到 INI 配置文件中。同时可以通过以上描述的 `-c` 选项对生成的文件进行编辑。

## 更新 sqlmap

开关：`--update`

使用此开关，你可以直接从 [Git 仓库](https://github.com/sqlmapproject/sqlmap.git) 将该工具升级到最新的开发版本。当然，网络连接必不可少。

当然，如果上面的操作失败，你可以直接在 sqlmap 所在目录运行 `git pull`。这样的执行效果和使用开关 `--update` 一样。如果你是在 Windows 上使用 sqlmap，可以使用 [SmartGit](http://www.syntevo.com/smartgit/index.html) 客户端。

在向[邮件列表](http://www.sqlmap.org/#ml)反馈任何潜在 bug **之前**，强烈建议先尝试上面描述的方法。
