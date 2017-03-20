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

---

### Force the database management system operating system name

Option: `--os`

By default sqlmap automatically detects the web application's back-end database management system underlying operating system when this information is a dependence of any other provided switch or option. At the moment the fully supported operating systems are:

* Linux
* Windows

It is possible to force the operating system name if you already know it so that sqlmap will avoid doing it itself.

Note that this option is **not** mandatory and it is strongly recommended to use it **only if you are absolutely sure** about the back-end database management system underlying operating system. If you do not know it, let sqlmap automatically identify it for you. 

### Force usage of big numbers for invalidating values

Switch: `--invalid-bignum`

In cases when sqlmap needs to invalidate original parameter value (e.g. `id=13`) it uses classical negation (e.g. `id=-13`). With this switch it is possible to force the usage of large integer values to fulfill the same goal (e.g. `id=99999999`).

### Force usage of logical operations for invalidating values

Switch: `--invalid-logical`

In cases when sqlmap needs to invalidate original parameter value (e.g. `id=13`) it uses classical negation (e.g. `id=-13`). With this switch it is possible to force the usage of boolean operations to fulfill the same goal (e.g. `id=13 AND 18=19`).

### Force usage of random strings for invalidating values

Switch: `--invalid-string`

In cases when sqlmap needs to invalidate original parameter value (e.g. `id=13`) it uses classical negation (e.g. `id=-13`). With this switch it is possible to force the usage of random strings to fulfill the same goal (e.g. `id=akewmc`).

### Turn off payload casting mechanism

Switch: `--no-cast`

When retrieving results, sqlmap uses a mechanism where all entries are being casted to string type and replaced with a whitespace character in case of `NULL` values. That is being made to prevent any erroneous states (e.g. concatenation of `NULL` values with string values) and to easy the data retrieval process itself. Nevertheless, there are reported cases (e.g. older versions of MySQL DBMS) where this mechanism needed to be turned-off (using this switch) because of problems with data retrieval itself (e.g. `None` values are returned back).

### Turn off string escaping mechanism

Switch: `--no-escape`

In cases when sqlmap needs to use (single-quote delimited) string values inside payloads (e.g. `SELECT 'foobar'`), those values are automatically being escaped (e.g. `SELECT CHAR(102)+CHAR(111)+CHAR(111)+CHAR(98)+CHAR(97)+CHAR(114)`). That is being done because of two things: obfuscation of payload content and preventing potential problems with query escaping mechanisms (e.g. `magic_quotes` and/or `mysql_real_escape_string`) at the back-end server. User can use this switch to turn it off (e.g. to reduce payload size).

### Custom injection payload

Options: `--prefix` and `--suffix`

In some circumstances the vulnerable parameter is exploitable only if the user provides a specific suffix to be appended to the injection payload. Another scenario where these options come handy presents itself when the user already knows that query syntax and want to detect and exploit the SQL injection by directly providing a injection payload prefix and suffix. 

Example of vulnerable source code:

    $query = "SELECT * FROM users WHERE id=('" . $_GET['id'] . "') LIMIT 0, 1";

To detect and exploit this SQL injection, you can either let sqlmap detect the **boundaries** (as in combination of SQL payload prefix and suffix) for you during the detection phase, or provide them on your own.

For example: 

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/mysql/get_str_brackets.php\
?id=1" -p id --prefix "')" --suffix "AND ('abc'='abc"
[...]
```

This will result in all sqlmap requests to end up in a query as follows:

    $query = "SELECT * FROM users WHERE id=('1') <PAYLOAD> AND ('abc'='abc') LIMIT 0, 1";

Which makes the query syntactically correct.

In this simple example, sqlmap could detect the SQL injection and exploit it without need to provide custom boundaries, but sometimes in real world application it is necessary to provide it when the injection point is within nested `JOIN` queries for instance. 

### Tamper injection data

Option: `--tamper`

sqlmap itself does no obfuscation of the payload sent, except for strings between single quotes replaced by their `CHAR()`-alike representation. 

This option can be very useful and powerful in situations where there is a weak input validation mechanism between you and the back-end database management system. This mechanism usually is a self-developed input validation routine called by the application source code, an expensive enterprise-grade IPS appliance or a web application firewall (WAF). All buzzwords to define the same concept, implemented in a different way and costing lots of money, usually. 

To take advantage of this option, provide sqlmap with a comma-separated list of tamper scripts and this will process the payload and return it transformed. You can define your own tamper scripts, use sqlmap ones from the `tamper/` folder or edit them as long as you concatenate them comma-separated as value of the option `--tamper` (e.g. `--tamper="between,randomcase"`). 

The format of a valid tamper script is as follows:

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

You can check valid and usable tamper scripts in the `tamper/` directory.

Example against a MySQL target assuming that `>` character, spaces and capital `SELECT` string are banned:

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