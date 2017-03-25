## 注入

以下选项用于指定要测试的参数，提供自定义注入 payloads 和可选篡改脚本。

### 可测试参数

选项：`-p`，`--skip` 和 `--param-exclude`

默认情况下 sqlmap 会测试所有 GET 参数和 POST 参数。当 `--level` 的值 >= **2**，它还会测试 HTTP `Cookie` 头值。当这个值 >= **3** 时，它还会测试 HTTP `User-Agent` 和 HTTP `Referer` 头值。而且还可以手动指定一个需要 sqlmap 进行测试的逗号分隔的参数列表。这将忽略对 `--level` 的设置。

例如，只需要测试 GET 参数 `id` 和 HTTP `User-Agent`，则提供 `-p "id,user-agent"`。

如果用户想要排除测试某些参数，他可以使用选项 `--skip`。这在你想对 `--level` 使用更高的级别并测试所有可用参数，而不包括通常被测试的一些 HTTP 头时是非常有用的。

例如，要在 `--level=5` 跳过测试 HTTP `User-Agent` 和 HTTP `Referer`，可以提供 `--skip="user-agent,referer"`。

还可以基于正则表达式针对参数名称来排除某些参数的测试。在这种情况下，用户可以使用选项 `--param-exclude`。

例如，要跳过对名称中包含 `token` 或 `session` 的参数的测试，可以提供 `--param-exclude="token|session"`。

#### URI 注入点

有一些特殊情况是注入点处于 URI 本身内。除非手动指定，sqlmap 不会对 URI 路径执行任何自动测试。你需要在命令行中标明这些注入点，通过在每个需要 sqlmap 测试和利用 SQL 注入的 URI 点后面附加一个星号（`*`）（注意：也支持 Havij 风格 `%INJECT HERE%`）。

例如，当使用了 Apache Web 服务器的 [mod_rewrite](http://httpd.apache.org/docs/current/mod/mod_rewrite.html)模块或其他类似的技术时，这是特别有用的。

一个有效的命令行例子如下：

```
$ python sqlmap.py -u "http://targeturl/param1/value1*/param2/value2/"
```

#### 任意注入点

与 URI 注入点类似，星号（`*`）（注意：也支持 Havij 风格 `%INJECT HERE%`）也可以用于指向 GET，POST 或 HTTP 头中的任意注入点。可以在选项 `-u` 提供的 GET 参数值，选项 `--data` 提供的 POST 参数值，选项 `-H` 提供的 HTTP 头值如 `--headers`，`--user-agent`，`--referer` 和/或 `--cookie`，或从文件加载的 HTTP 请求中的通用位置这些地方的内部指定注入点。

一个有效的命令行例子如下：

```
$ python sqlmap.py -u "http://targeturl" --cookie="param1=value1*;param2=value2"
```

### 指定 DBMS 类型

选项：`--dbms`

默认情况下 sqlmap 会自动检测 Web 应用程序的后端数据库管理系统。sqlmap 完全支持以下数据库管理系统：

* MySQL
* Oracle
* PostgreSQL
* Microsoft SQL Server
* Microsoft Access
* IBM DB2
* SQLite
* Firebird
* Sybase
* SAP MaxDB
* HSQLDB
* Informix

如果由于某些原因 sqlmap 已经识别出 SQL 注入却无法检测到后端 DBMS 类型，或者你想避免执行指纹信息收集，你可以自己提供后端 DBMS 的名称（例如：`postgresql`）。对于 MySQL 和 Microsoft SQL Server 分别以 `MySQL <version>` 和 `Microsoft SQL Server <version>` 的形式提供，其中 `<version>`是指 DBMS 的有效版本；例如 MySQL 为 5.0，Microsoft SQL Server 为 2005。

如果你同时使用 `--dbms` 和 `--fingerprint`，sqlmap 将只对指定的数据库管理系统执行大量指纹收集，更详细的信息请阅读下文。

注意，此选项是**不**是强制性的，强烈建议**仅当你绝对确定**后端数据库管理系统时使用它。如果你不知道，就让 sqlmap 自动为你收集指纹信息。

### 指定 DBMS 操作系统名称

选项：`--os`

默认情况下，当此信息是任何开关或选项的依赖时，sqlmap 会自动检测 Web 应用程序后端 DBMS 的底层操作系统信息。 目前完全支持的操作系统有：

* Linux
* Windows

你可以强制指定已知的操作系统类型，sqlmap 将避免对该信息进行检测。

注意，此选项**不**是强制性的，强烈建议**仅当您绝对确定**后端 DBMS 底层操作系统时使用它。如果你不知道，就让 sqlmap 自动为你识别。

### 强制使用大数字来使参数值无效

开关：`--invalid-bignum`

在 sqlmap 需要使原始参数值无效（例如：`id=13`）的情况下，它会使用负数（例如：`id=-13`）。使用此开关可以强制使用大整数值来达到一样的效果（例如：`id=99999999`）。

### 强制使用逻辑运算使值无效

开关：`--invalid-logical`

在 sqlmap 需要使原始参数值无效（例如：`id=13`）的情况下，它会使用负数（例如：`id=-13`）。使用此开关可以强制使用布尔运算来达到一样的效果（例如：`id=13 AND 18=19`）。

### 强制使用随机字符串使值无效

开关：`--invalid-string`

在 sqlmap 需要使原始参数值无效（例如：`id=13`）的情况下，它会使用负数（例如：`id=-13`）。使用此开关可以强制使用随机字符串来达到一样的效果（例如：`id=akewmc`）。

### 关闭 payload 构造机制

开关：`--no-cast`

检索结果时，sqlmap 使用一种机制，所有条目都被转换为字符串类型，并使用空格字符替换 `NULL` 值。这样做是为了避免任何错误的状态（例如：使用字符串连接 `NULL` 值）并简化数据检索过程本身。然而，根据报告有些情形（例如：MySQL DBMS 的旧版本）由于数据检索本身存在问题（例如：返回了 `None` 值），需要关闭此机制（使用此开关）。

### 关闭字符串转义机制

开关：`--no-escape`

在 sqlmap 需要使用（单引号分隔的）payloads 里的字符串（例如：`SELECT 'foobar'`）的情况下，这些值将被自动转义（例如：`SELECT CHAR(102)+CHAR(111)+CHAR(111)+CHAR(98)+CHAR(97)+CHAR(114)`（译者注：该例语法属于 Microsoft SQL Server））。这么做有两个原因：对 payload 内容进行模糊处理，还有防止后端服务器上潜在的查询转义机制（例如：`magic_quotes` 和/或 `mysql_real_escape_string`）。用户可以使用此开关将其关闭（例如：减少 payload 的大小）。

### 自定义注入 payload

选项：`--prefix` 和 `--suffix`

在某些情况下，仅当用户提供要附加到注入 payload 的特定后缀时，才能利用易受攻击的参数。当用户已经知道查询语法并希望通过直接提供注入 payload 前缀和后缀来检测并利用 SQL 注入时，这些选项对这种场景会很方便。

漏洞源代码示例：

    $query = "SELECT * FROM users WHERE id=('" . $_GET['id'] . "') LIMIT 0, 1";

要检测并利用此 SQL 注入，您可以让 sqlmap 在检测阶段检测**边界**（与 SQL payload 前缀和后缀组合），或者自己提供它们。

例如：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/get_str_brackets.php\
?id=1" -p id --prefix "')" --suffix "AND ('abc'='abc"
[...]
```

这将使所有 sqlmap 请求最终构成以下查询：

    $query = "SELECT * FROM users WHERE id=('1') <PAYLOAD> AND ('abc'='abc') LIMIT 0, 1";

以使查询语法正确。

在这个简单的例子中，sqlmap 可以检测 SQL 注入并利用它，而不需要提供自定义的边界，但有时在真实情况中的应用程序，当注入点存在于嵌套的 `JOIN` 查询中时，需要提供它。

### 修改注入数据

选项：`--tamper`

sqlmap 本身不会混淆发送的 payload，除了处于单引号之间被 诸如 `CHAR()` 替换的字符串。

在你和后端 DBMS 之间存在弱输入验证机制的情况下，此选项会非常有用。这种验证机制通常是由应用程序源代码调用自行开发的输入验证例程，如昂贵的企业级 IPS 设备或 Web 应用程序防火墙（WAF）。一言蔽之，它们通常以不同的方式实现并花费大量资金。

要利用此选项，需要为 sqlmap 提供逗号分隔的修改脚本列表，这将处理 payload 并将其转换。你可以定义自己的修改脚本，使用 sqlmap `tamper/` 文件夹中的脚本，或者使用逗号分隔连接它们作为 `--tamper` 选项的值（例如：`--tamper="between,randomcase"`）。

有效的修改脚本格式如下：

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ {.python}
# Needed imports
from lib.core.enums import PRIORITY

# Define which is the order of application of tamper scripts against
# the payload
__priority__ = PRIORITY.NORMAL

def tamper(payload):
    '''
    Description of your tamper script
    '''

    retVal = payload

    # your code to tamper the original payload

    # return the tampered payload
    return retVal
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 

你可以在 `tamper/` 目录中查看有效和可用的修改脚本。

假定字符 `>`，空格和大写的 `SELECT` 字符串被禁止：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/get_int.php?id=1" --\
tamper tamper/between.py,tamper/randomcase.py,tamper/space2comment.py -v 3

[hh:mm:03] [DEBUG] cleaning up configuration parameters
[hh:mm:03] [INFO] loading tamper script 'between'
[hh:mm:03] [INFO] loading tamper script 'randomcase'
[hh:mm:03] [INFO] loading tamper script 'space2comment'
[...]
[hh:mm:04] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[hh:mm:04] [PAYLOAD] 1)/**/And/**/1369=7706/**/And/**/(4092=4092
[hh:mm:04] [PAYLOAD] 1)/**/AND/**/9267=9267/**/AND/**/(4057=4057
[hh:mm:04] [PAYLOAD] 1/**/AnD/**/950=7041
[...]
[hh:mm:04] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE or HAVING clause
'
[hh:mm:04] [PAYLOAD] 1/**/anD/**/(SELeCt/**/9921/**/fROm(SELeCt/**/counT(*),CONC
AT(cHar(58,117,113,107,58),(SELeCt/**/(case/**/whEN/**/(9921=9921)/**/THeN/**/1/
**/elsE/**/0/**/ENd)),cHar(58,106,104,104,58),FLOOR(RanD(0)*2))x/**/fROm/**/info
rmation_schema.tables/**/group/**/bY/**/x)a)
[hh:mm:04] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE or
 HAVING clause' injectable 
[...]
```