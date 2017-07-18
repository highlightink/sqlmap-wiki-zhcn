## 目标

至少提供以下其中一个选项，用于指定目标。

### 直连数据库

选项：`-d`

针对单一数据库实例运行 sqlmap 工具。这个选项可设置为下面格式的连接字符串：

* `DBMS://USER:PASSWORD@DBMS_IP:DBMS_PORT/DATABASE_NAME`（MySQL，Oracle，Microsoft SQL Server，PostgreSQL 等。)
* `DBMS://DATABASE_FILEPATH`（SQLite，Microsoft Access，Firebird 等。）

例如：

```
$ python sqlmap.py -d "mysql://admin:admin@192.168.21.17:3306/testdb" -f --bann\
er --dbs --users
```

### 目标 URL

选项：`-u` 或 `--url`

针对单一目标 URL 运行 sqlmap。这个选项可设置为下面格式的 URL：

`http(s)://targeturl[:port]/[...]`

例如：

```
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1" -f --banner --dbs -\
-users
```

### 从 Burp 或 WebScarab 代理日志解析目标

选项：`-l`

除了可以提供单个目标 URL，还可以测试并注入 [Burp 代理](http://portswigger.net/suite/)或者 [WebScarab 代理](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project)代理的 HTTP 请求。使用这个参数时，需要提供代理 HTTP 请求的日志文件。
 
### 从远程站点地图（.xml）解析目标

选项：`-x`

通过站点地图，站点管理者可以列出网站的所有页面位置，用于告知搜索引擎站点的内容结构。你可以使用选项 `-x` 提供站点地图地址给 sqlmap（例如：`-x http://www.target.com/sitemap.xml`）以搜寻可用的目标 URLs。

### 从给定的文本文件读取多个目标进行扫描

选项：`-m`

通过文本文件提供一个目标 URLs 列表，sqlmap 会逐个进行扫描检测。

样本文件所提供的 URLs 列表示例：

    www.target1.com/vuln1.php?q=foobar
    www.target2.com/vuln2.asp?id=1
    www.target3.com/vuln3/id/1*

### 从文件中载入 HTTP 请求

选项：`-r`

sqlmap 可以从一个文本中读取原始的 HTTP 请求。通过这种方式，你能够免于设置部分选项（例如：设置 cookies，POST 数据等参数）。

HTTP 请求文件数据样本如下：

    POST /vuln.php HTTP/1.1
    Host: www.target.com
    User-Agent: Mozilla/4.0
    
    id=1

如果相关的请求是 HTTPS，你可以结合 `--force-ssl` 开关强制使用 SSL 进行 443/tcp 连接。或者，你可以在 `Host` 头部信息后面直接加上 `:443`。  

### 使用 Google dork 结果作为目标地址

选项：`-g`

sqlmap 同时支持根据 Google dork 返回结果测试并注入 GET 参数。

这个选项使得 sqlmap 能够和搜索引擎当前会话 cookies 进行内容交互，进行相关的搜索操作。然后 sqlmap 会获取 Google dork 表达式筛选出的前 100 个返回结果及附带的 GET 参数，并且询问你是否对每个可能存在注入的 URL 进行测试注入。

例如：

```
$ python sqlmap.py -g "inurl:\".php?id=1\""
```

### 从 INI 配置文件中读取选项

选项：`-c`

sqlmap 支持从 INI 配置文件中读取用户的选项配置，例如：`sqlmap.conf`。

需要注意的是，如果你在命令行调用时，同时提供了相关的选项设置，则配置文件中的选项会被覆盖失效。