# 特性

sqlmap 实现的功能特性包括：

## 通用特性
* 完全支持 **MySQL**，**Oracle**，**PostgresSQL**，**Microsoft SQL Server**，**Microsoft Access**，**IBM DB2**，**SQLite**，**Firebird**，**Sybase**，**SAP MaxDB** 和 **HSQLDB** 数据库管理系统。
* 完全支持五种 SQL 注入技术：**布尔型盲注**，**时间型盲注**，**报错型注入**，**联合查询注入**和**堆查询注入**。
* 支持通过提供 DBMS 凭证，IP 地址，端口和数据库名而非 SQL 注入**直接连接数据库**。
* 支持用户提供的单个目标 URL，通过 [Burp proxy](http://portswigger.net/suite/) 或 [WebScarab proxy](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project) 的请求日志文件批量获取目标地址，从文本文件获得完整的 HTTP 请求报文或使用 Google dork ——使用 [Google](http://www.google.com) 查询并解析结果页面获取批量目标。也可以自定义基于范围的正则表达式。
* 可以测试并利用 **GET** 和 **POST** 参数，HTTP 头中的 **Cookie**，**User-Agent** 和 **Referer** 这些地方出现的 SQL 注入漏洞。也可以指定用英文逗号隔开的一个参数列表进行测试。
* 支持自定义**最大 HTTP(S) 并发请求数（多线程）**以提高盲注的速度。反之亦然，还可以设置每个 HTTP(S) 请求的间隔时间（秒）。当然，还有其他用来提高测试速度的相关优化选项。
* 支持 **HTTP 头中的 `Cookie`**，当目标 Web 应用的身份验证基于 cookies，而你有这项数据，或者你就是想要对 cookies 这个头部参数测试和利用 SQL 注入时，这个功能是非常有用的。你还可以指定总是对 Cookie 进行 URL 编码。
* 自动处理来自 Web 应用的 **HTTP `Set-Cookie`** 消息，并重建超时的会话。这个参数也可以被测试并利用。反之亦然，你可以强制忽略任何 `Set-Cookie` 消息头。
* 支持 HTTP **Basic，Digest，NTLM 和 Certificate authentications** 协议。
* **HTTP(S) proxy** support to pass by the requests to the target application that works also with HTTPS requests and with authenticated proxy servers.（实在翻不来！！！！！！！！！！）
* 支持伪造 **HTTP `Referer`** 和 **HTTP `User-Agent`**，可通过用户或者从一个文本文件中随机指定。
* 支持设置**输出信息的详细级别**：共有**七个级别**的详细程度。
* 支持从目标 URL 中**解析 HTML 表单**并伪造 HTTP(S) 请求以测试这些表单参数是否存在漏洞。
* 通过用户设置选项调整**粒度和灵活性**。
* 对每一次查询实时**评估完成时间**，使用户能知道输出结果需要的大概时长。
* 在抓取数据时能实时自动保存将会话（包括完整的和完整的查询与其对应结果）保存在一个文本文件中，并通过解析会话文件**继续进行注入检测**。
* 支持从 INI 配置文件中读取相关选项而不是每次都要在命令行中设置。支持从命令行中生成配置文件。
* 支持**复制后端数据库表结构和数据项**到本地的 SQLite 3 数据库中。
* 支持从 SVN 仓库中将 sqlmap 升级到最新的开发版本。
* 支持解析 HTTP(S) 响应并显示出 DBMS 错误信息。
* 集成其他 IT 开源安全项目，[Metasploit](http://metasploit.com) 和 [w3af](http://w3af.sourceforge.net)。

## 采集指纹和枚举功能

* 根据[错误信息](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html)，[banner 解析](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html)，[函数输出对比](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html) 和 [特定特征](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html) 例如 MySQL 注释注入收集**广泛的数据库版本和底层操作系统指纹信息**。如果你已经知道数据库系统的版本，则可以手动指定它。
* 支持采集基本的 Web 服务器和 Web 应用技术的指纹信息。
* 支持获取 DBMS **banner**，**会话用户**和**当前数据库**等信息。sqlmap 还能检查当前会话用户是否为数据库管理员帐号。
* 支持枚举**用户，密码散列，特权级，角色，数据库，表和列**。
* 支持自动识别密码散列格式并**用字典攻击破解**。
* 支持**暴力猜解表名和列名**。当会话用户没有读取系统表的权限或数据库没有存储表信息（例如 MySQL < 5.0）时这项功能会非常有用。
* 支持完整地 **dump 数据表**，获取某个范围内的数据项或根据用户指定的列。The user can also choose to dump only a range of characters from each column's entry.
* Support to automatically **dump all databases**' schemas and entries. It is possibly to exclude from the dump the system databases.
* 支持**搜索指定的数据库名，在数据库中搜索表，或在所有表中搜索列名**。这是一项很有用的功能，例如，通过搜索列名中包含像 **name** 和 **pass** 的表来确定哪些表包含用户敏感信息。
* 支持像交互式 SQL 客户端一样连接数据库并**执行 SQL 语句**。sqlmap 会自动分析用户所提供的 SQL 语句，以此决定用最合适的技术去注入并打包相应的 SQL payload。

## 接管功能

以下的相关技术详细信息可在白皮书 [高级 SQL 注入到完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857) 和 幻灯片 [将控制权从数据库扩展到操作系统](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database) 中找到。

* 支持**注入用户自定义函数**：用户可以编译共享库并通过 sqlmap 在数据库中创建共享库中没有的用户自定义函数。可以通过 sqlmap 执行或者移除这些 UDFs。这些功能支持 MySQL 和 PostgreSQL 数据库。
* 支持从 MySQL，PostgreSQL 和 Microsoft SQL Server 服务器的文件系统中**下载和上传文件**。
* 支持在 MySQL，PostgreSQL 和 Microsoft SQL Server 服务器的操作系统中**执行任意命令并获取相应输出**。
* On MySQL and PostgreSQL via user-defined function injection and execution.
* On Microsoft SQL Server via `xp_cmdshell()` stored procedure. Also, the stored procedure is re-enabled if disabled or created from scratch if removed by the DBA.
* 支持在操作系统中**建立攻击者机器和数据库服务器之间的有状态的带外数据 TCP 连接**。根据用户选择这个通道可以是交互式命令行提示符，Meterpreter 会话或图形用户界面（VNC）会话。sqlmap 依赖 Metasploit 生成 shellcode 并通过四种技术在数据库服务器执行。这些技术分别是：
1. 通过 sqlmap 使用用户自定义的 `sys_bineval()` 函数**在内存中执行 Metasploit shellcode**。支持 MySQL 和 PostgreSQL。
2. 对于 MySQL 和 PostgreSQL，通过 sqlmap 使用用户自定义的 `sys_exec()` 函数上传并执行一个 Metasploit 的**独立运行的 payload**，对于 Microsoft SQL Server 则使用 `xp_cmdshell()`。
3. 通过 **SMB 反射攻击**（[MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx)）执行 Metasploit shellcode，这需要目标数据库服务器向已被 Metasploit `smb_relay` 监听的攻击者机器发出一个 UNC 路径请求。当 sqlmap 运行在 Linux/Unix 高权限（`uid=0`）并且目标 DMBS 在 Windows 中以管理员身份运行时支持该功能。
4. 通过利用 **Microsoft SQL Server 2000 和 2005 中存在的 `sp_replwritetovarbin` 存储过程堆缓冲区溢出**（[MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)）在内存中执行 Metasploit shellcode。sqlmap 有自己的利用程序通过自动绕过 DEP 内存保护去触发这个漏洞，但是它依赖 Metasploit 生成 shellcode 以执行攻击。
* 支持通过 Metasploit 的 `getsystem` 命令进行**数据库进程用户提权**，这个命令使用了 [kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) 技术（[MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx)）。
* 支持访问（读取/添加/删除）Windows 注册表项目。

## 演示

你可以在 [Bernardo](http://www.youtube.com/user/inquisb/videos) 和 [Miroslav](http://www.youtube.com/user/stamparm/videos) 的 YouTube 页面中观看演示。你也可以在[这里](http://unconciousmind.blogspot.com/search/label/sqlmap)找到大量专门用于合法 Web 测试的带有漏洞的 Web 应用。

---

# Features

Features implemented in sqlmap include:

## Generic features

* Full support for **MySQL**, **Oracle**, **PostgreSQL**, **Microsoft SQL Server**, **Microsoft Access**, **IBM DB2**, **SQLite**, **Firebird**, **Sybase**, **SAP MaxDB** and **HSQLDB** database management systems.
* Full support for five SQL injection techniques: **boolean-based blind**, **time-based blind**, **error-based**, **UNION query** and **stacked queries**.
* Support to **directly connect to the database** without passing via a SQL injection, by providing DBMS credentials, IP address, port and database name.
* It is possible to provide a single target URL, get the list of targets from [Burp proxy](http://portswigger.net/suite/) or [WebScarab proxy](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project) requests log files, get the whole HTTP request from a text file or get the list of targets by providing sqlmap with a Google dork which queries [Google](http://www.google.com) search engine and parses its results page. You can also define a regular-expression based scope that is used to identify which of the parsed addresses to test.
* Tests provided **GET** parameters, **POST** parameters, HTTP **Cookie** header values, HTTP **User-Agent** header value and HTTP **Referer** header value to identify and exploit SQL injection vulnerabilities. It is also possible to specify a comma-separated list of specific parameter(s) to test.
* Option to specify the **maximum number of concurrent HTTP(S) requests (multi-threading)** to speed up the blind SQL injection techniques. Vice versa, it is also possible to specify the number of seconds to hold between each HTTP(S) request. Others optimization switches to speed up the exploitation are implemented too.
* **HTTP `Cookie` header** string support, useful when the web application requires authentication based upon cookies and you have such data or in case you just want to test for and exploit SQL injection on such header values. You can also specify to always URL-encode the Cookie.
* Automatically handles **HTTP `Set-Cookie` header** from the application, re-establishing of the session if it expires. Test and exploit on these values is supported too. Vice versa, you can also force to ignore any `Set-Cookie` header.
* HTTP protocol **Basic, Digest, NTLM and Certificate authentications** support.
* **HTTP(S) proxy** support to pass by the requests to the target application that works also with HTTPS requests and with authenticated proxy servers.
* Options to fake the **HTTP `Referer` header** value and the **HTTP `User-Agent` header** value specified by user or randomly selected from a textual file.
* Support to increase the **verbosity level of output messages**: there exist **seven levels** of verbosity.
* Support to **parse HTML forms** from the target URL and forge HTTP(S) requests against those pages to test the form parameters against vulnerabilities.
* **Granularity and flexibility** in terms of both user's switches and features.
* **Estimated time of arrival** support for each query, updated in real time, to provide the user with an overview on how long it will take to retrieve the queries' output.
* Automatically saves the session (queries and their output, even if partially retrieved) on a textual file in real time while fetching the data and **resumes the injection** by parsing the session file.
* Support to read options from a configuration INI file rather than specify each time all of the switches on the command line. Support also to generate a configuration file based on the command line switches provided.
* Support to **replicate the back-end database tables structure and entries** on a local SQLite 3 database.
* Option to update sqlmap to the latest development version from the subversion repository.
* Support to parse HTTP(S) responses and display any DBMS error message to the user.
* Integration with other IT security open source projects, [Metasploit](http://metasploit.com) and [w3af](http://w3af.sourceforge.net).

## Fingerprint and enumeration features

* **Extensive back-end database software version and underlying operating system fingerprint** based upon
[error messages](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html),
[banner parsing](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html),
[functions output comparison](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html) and [specific features](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html) such as MySQL comment injection. It is also possible to force the back-end database management system name if you already know it.
* Basic web server software and web application technology fingerprint.
* Support to retrieve the DBMS **banner**, **session user** and **current database** information. The tool can also check if the session user is a **database administrator** (DBA).
* Support to enumerate **users, password hashes, privileges, roles, databases, tables and columns**.
* Automatic recognition of password hashes format and support to **crack them with a dictionary-based attack**.
* Support to **brute-force tables and columns name**. This is useful when the session user has no read access over the system table containing schema information or when the database management system does
not store this information anywhere (e.g. MySQL < 5.0).
* Support to **dump database tables** entirely, a range of entries or specific columns as per user's choice. The user can also choose to dump only a range of characters from each column's entry.
* Support to automatically **dump all databases**' schemas and entries. It is possibly to exclude from the dump the system databases.
* Support to **search for specific database names, specific tables across all databases or specific columns across all databases' tables**. This is useful, for instance, to identify tables containing custom application credentials where relevant columns' names contain string like **name** and **pass**.
* Support to **run custom SQL statement(s)** as in an interactive SQL client connecting to the back-end database. sqlmap automatically dissects the provided statement, determines which technique fits best to inject it and how to pack the SQL payload accordingly.

## Takeover features

Some of these techniques are detailed in the white paper
[Advanced SQL injection to operating system full control](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857) and in the slide deck [Expanding the control over the operating system from the database](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database).

* Support to **inject custom user-defined functions**: the user can compile a shared library then use sqlmap to create within the back-end DBMS user-defined functions out of the compiled shared library file. These
UDFs can then be executed, and optionally removed, via sqlmap. This is supported when the database software is MySQL or PostgreSQL.
* Support to **download and upload any file** from the database server underlying file system when the database software is MySQL, PostgreSQL or Microsoft SQL Server.
* Support to **execute arbitrary commands and retrieve their standard output** on the database server underlying operating system when the database software is MySQL, PostgreSQL or Microsoft SQL Server.
* On MySQL and PostgreSQL via user-defined function injection and execution.
* On Microsoft SQL Server via `xp_cmdshell()` stored procedure.
Also, the stored procedure is re-enabled if disabled or created from scratch if removed by the DBA.
* Support to **establish an out-of-band stateful TCP connection between the attacker machine and the database server** underlying operating system. This channel can be an interactive command prompt, a Meterpreter session or a graphical user interface (VNC) session as per user's choice.
sqlmap relies on Metasploit to create the shellcode and implements four different techniques to execute it on the database server. These techniques are:
* Database **in-memory execution of the Metasploit's shellcode** via sqlmap own user-defined function `sys_bineval()`. Supported on MySQL and PostgreSQL.
* Upload and execution of a Metasploit's **stand-alone payload stager** via sqlmap own user-defined function `sys_exec()` on MySQL and PostgreSQL or via `xp_cmdshell()` on Microsoft SQL Server.
* Execution of Metasploit's shellcode by performing a **SMB reflection attack** ([MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx) with a UNC path request from the database server to the attacker's machine where the Metasploit `smb_relay` server exploit listens. Supported when running sqlmap with high privileges (`uid=0`) on Linux/Unix and the target DBMS runs as Administrator on Windows.
* Database in-memory execution of the Metasploit's shellcode by exploiting **Microsoft SQL Server 2000 and 2005 `sp_replwritetovarbin` stored procedure heap-based buffer overflow** ([MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)). sqlmap has its own exploit to trigger the vulnerability with automatic DEP memory protection bypass, but it relies on Metasploit to generate the shellcode to get executed upon successful exploitation.
* Support for **database process' user privilege escalation** via Metasploit's `getsystem` command which include, among others, the 
[kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) technique ([MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx)).
* Support to access (read/add/delete) Windows registry hives.

## Demo

You can watch demo videos on [Bernardo](http://www.youtube.com/user/inquisb/videos) and [Miroslav](http://www.youtube.com/user/stamparm/videos) YouTube pages. Also, you can find lots of examples against publicly available vulnerable web applications made for legal web assessment [here](http://unconciousmind.blogspot.com/search/label/sqlmap).
