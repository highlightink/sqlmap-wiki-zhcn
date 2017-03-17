## 目标

至少提供下面的一个选项，用于指定目标。

### 直连数据库
选项: `-d`

针对单一数据库实例运行 sqlmap 工具。这个选项可设置为下面格式的连接字符串：

* `DBMS://USER:PASSWORD@DBMS_IP:DBMS_PORT/DATABASE_NAME` (MySQL, Oracle, Microsoft SQL Server, PostgreSQL, etc.)
* `DBMS://DATABASE_FILEPATH` (SQLite, Microsoft Access, Firebird, etc.)

例如:
```
$ python sqlmap.py -d "mysql://admin:admin@192.168.21.17:3306/testdb" -f --bann\
er --dbs --users
```

### 目标 URL
选项: -u or --url

针对单一目标 URL 运行 sqlmap。这个选项可设置为下面格式的 URL：

`http(s)://targeturl[:port]/[...]`

例如:

```
$ python sqlmap.py -u "http://www.target.com/vuln.php?id=1" -f --banner --dbs -\
-users
```

### 从 Burp 或者 WebScarab 代理日志解析目标
选项: `-l`

除了可以提供单个目标 URL，还可以测试并注入 [Burp proxy](http://portswigger.net/suite/)  或者 [WebScarab proxy](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project) 代理的HTTP代理请求。 使用这个参数时，需要提供代理 HTTP 请求的日志文件。
 
### 从远程站点地图(.xml)解析目标
选项: `-x`

通过站点地图，站点管理者可以列出网站的所有页面位置，用于告知搜索引擎站点的内容结构。你可以使用选项 `-x` 提供站点地图位置给 sqlmap (例如：. `-x` http://www.target.com/sitemap.xml)， 用于可入侵目标站点的扫描。

### Scan multiple targets enlisted in a given textual file

Option: `-m`

Providing list of target URLs enlisted in a given bulk file, sqlmap will scan 
each of those one by one.

Sample content of a bulk file provided as an argument to this option:

    www.target1.com/vuln1.php?q=foobar
    www.target2.com/vuln2.asp?id=1
    www.target3.com/vuln3/id/1*

### Load HTTP request from a file

Option: `-r`

One of the possibilities of sqlmap is loading of raw HTTP request from a textual file. That way you can skip usage of a number of other options (e.g. setting of cookies, POSTed data, etc).

Sample content of a HTTP request file provided as an argument to this option:

    POST /vuln.php HTTP/1.1
    Host: www.target.com
    User-Agent: Mozilla/4.0
    
    id=1

Note that if the request is over HTTPS, you can use this in conjunction with switch `--force-ssl` to force SSL connection to 443/tcp. Alternatively, you can append `:443` to the end of the `Host` header value.

### Process Google dork results as target addresses

Option: `-g`

It is also possible to test and inject on GET parameters based on results of your Google dork.

This option makes sqlmap negotiate with the search engine its session cookie to be able to perform a search, then sqlmap will retrieve Google first 100 results for the Google dork expression with GET parameters asking you if you want to test and inject on each possible affected URL.

For example:

```
$ python sqlmap.py -g "inurl:\".php?id=1\""
```

### Load options from a configuration INI file

Option: `-c`

It is possible to pass user's options from a configuration INI file, an example is `sqlmap.conf`.

Note that if you provide other options from command line, those are evaluated when running sqlmap and overwrite those provided in the configuration file.
