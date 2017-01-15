# 介绍

## 检测和利用 SQL 注入漏洞

假设你正在进行web 应用审计，发现有某个web 页面接受来自用户端提供的动态数据，这些数据通过 `GET`，`POST`或`Cookie` 参数或 HTTP `User-Agent` 请求头发送。  
因而，你想测试通过参数是否可以构造出 SQL 注入漏洞，如果有漏洞，则可以利用它们从后端的数据库系统中获取尽可能多的信息，甚至进一步控制更加底层的文件系统和操作系统。

简而言之，考虑下面的 url：

```
http://192.168.136.131/sqlmap/mysql/get_int.php?id=1
```

假设：

```
http://192.168.136.131/sqlmap/mysql/get_int.php?id=1+AND+1=1
```
    
页面显示跟原来的一样（AND+1=1 条件取值为 **True**（译者注：url 编码中 `+` 会被转成空格）），而：

```
http://192.168.136.131/sqlmap/mysql/get_int.php?id=1+AND+1=2
```  

页面显示跟原来的不一样（AND+1=2 条件取值为 **False**）。这可能说明 `index.php` 的 `id` `GET` 参数存在 SQL 注入漏洞。此外，这种情形也表明用户输入的数据在 SQL 语句传送到数据库之前没有被过滤。

这种设计缺陷在动态网页应用中十分常见，此类型漏洞与后端数据管理系统或后端编程语言并没有关系，漏洞的引入通常存在于代码的编写逻辑里面。从2013年开始，[开放 Web 应用安全组织](http://www.owasp.org)已将此类漏洞列入[最常见](https://owasptop10.googlecode.com/files/OWASP%20Top%2010%20-%202013.pdf)严重Web应用漏洞的前十名单。

从上面的例子我们发现了存在可以利用的参数，现在我们可以通过在每一次的 HTTP 请求中修改 `id` 进行相关的漏洞检测。

重回上面的情景，通过相关的经验，我们可以对 `get_ini.php` 页面中如何使用用户提供的参数值构建出相关的 `SELECT` 语句做一个大致的猜测。参考下面的 PHP 伪代码：

```
$query = "SELECT [column name(s)] FROM [table name] WHERE id=" . $_REQUEST['id'];
```

如你所见，通过在 `id` 参数后面添加合法并且布尔值为 **True** 的 SQL 语句（例如 `id=1 AND 1=1`），能够使 Web 应用返回和之前合法请求（没有添加其它的 SQL 语句）一模一样的页面。由此可见，后端数据库执行了先前我们注入的 SQL 语句。上面的例子展示了一个基于布尔值的简单 SQL 盲注。然而，通过使用 sqlmap 工具，我们可以检测出任意类型的 SQL 注入漏洞，并且将其纳入对应的工作流。

在这个简单的场景中不只是可以添加一个或多个 SQL 判断条件，还可以（取决于数据库系统类型）添加更多的 SQL 查询语句。例如： 
`[...]&id=1; 其他 SQL 查询语句#`。

sqlmap 能自动化地识别和利用这种漏洞。将这个地址输入到 sqlmap，`http://192.168.136.131/sqlmap/mysql/get_int.php?id=1`，它能自动地：

* 识别有漏洞的参数（比如这个例子中的 `id`）
* 判断可用哪种 SQL 注入技术去利用有漏洞的参数。
* 采集后端数据库系统的信息
* 根据用户使用的选项，它还能采集更多的信息，枚举数据或是完全地接管数据库系统

网上还有很多深入讲解如何检测，利用和防止 SQL 注入漏洞的[资源](http://delicious.com/inquis/sqlinjection)。推荐你在学习使用 sqlmap 之前阅读这些材料。

## 直接连接数据库系统

Up until sqlmap version **0.8**, the tool has been **yet another SQL injection tool**, used by web application penetration testers/newbies/curious teens/computer addicted/punks and so on. Things move on and as they evolve, we do as well. Now it supports this new switch, `-d`, that allows you to connect from your machine to the database server's TCP port where the database management system daemon is listening on and perform any operation you would do while using it to attack a database via a SQL injection vulnerability.

---
In this simple scenario it would also be possible to append, not just one or more valid SQL conditions, but also \(depending on the DBMS\) stacked SQL queries. For instance:  `[...]&id=1;ANOTHER SQL QUERY#`.

sqlmap can automate the process of identifying and exploiting this type of vulnerability. Passing the original address, `http://192.168.136.131/sqlmap/mysql/get_int.php?id=1` to sqlmap, the tool will automatically:

* Identify the vulnerable parameter\(s\) \(`id` in this example\)
* Identify which SQL injection techniques can be used to exploit the vulnerable parameter\(s\)
* Fingerprint the back-end database management system
* Depending on the user's options, it will extensively fingerprint, enumerate data or takeover the database server as a whole

...and depending on supplied options, it will enumerate data or takeover the database server entirely.

There exist many [resources](http://delicious.com/inquis/sqlinjection) on the web explaining in depth how to detect, exploit and prevent SQL injection vulnerabilities in web applications. It is recommendeded that you read them before going much further with sqlmap.

## Direct connection to the database management system

Up until sqlmap version **0.8**, the tool has been **yet another SQL injection tool**, used by web application penetration testers/newbies/curious teens/computer addicted/punks and so on. Things move on  
and as they evolve, we do as well. Now it supports this new switch, `-d`, that allows you to connect from your machine to the database server's TCP port where the database management system daemon is listening  
on and perform any operation you would do while using it to attack a database via a SQL injection vulnerability.

