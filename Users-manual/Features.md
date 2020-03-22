# 特性

> *译自：[Features](https://github.com/sqlmapproject/sqlmap/wiki/Features)*

sqlmap 实现的功能特性包括：

## 通用特性

* 完全支持 **MySQL**，**Oracle**，**PostgreSQL**，**Microsoft SQL Server**，**Microsoft Access**，**IBM DB2**，**SQLite**，**Firebird**，**Sybase**，**SAP MaxDB**， **Informix**，**MariaDB**，**Percona**，**MemSQL**，**TiDB**，**CockroachDB**，**HSQLDB**，**H2**，**MonetDB**，**Apache Derby**，**Vertica**，**Mckoi**，**Presto** 和 **Altibase** 等 DBMS（Database Management System，数据库管理系统）。
* 完全支持五种 SQL 注入技术：**布尔型盲注（Boolean-based blind）**，**时间型盲注（Time-based blind）**，**报错型注入（Error-based）**，**联合查询注入（UNION query-based）**和**堆叠查询（Stacked queries）注入**。
* 支持通过提供 DBMS 凭证，IP 地址，端口和数据库名而非 SQL 注入**直接连接数据库**。
* 支持用户提供单个目标 URL，通过 [Burp proxy](http://portswigger.net/suite/) 或 [WebScarab proxy](http://www.owasp.org/index.php/Category:OWASP_WebScarab_Project) 的请求日志文件批量获取目标地址列表，从文本文件获得完整的 HTTP 请求报文或使用 Google dork——使用 [Google](http://www.google.com) 查询并解析结果页面获取批量目标。也可以自定义正则表达式进行验证解析。
* 可以测试并利用 **GET** 和 **POST** 参数，HTTP 头中的 **Cookie**，**User-Agent** 和 **Referer** 这些地方出现的 SQL 注入漏洞。也可以指定一个用英文逗号隔开的参数列表进行测试。
* 支持自定义**最大 HTTP(S) 并发请求数（多线程）**以提高盲注的速度。同时，还可以设置每个 HTTP(S) 请求的间隔时间（秒）。当然，还有其他用来提高测试速度的相关优化选项。
* 支持设置 **HTTP 头中的** `Cookie`，当你需要为基于 cookies 身份验证的目标 Web 应用提供验证凭证，或者是你想要对 cookies 这个头部参数进行测试和利用 SQL 注入时，这个功能是非常有用的。你还可以指定对 Cookie 进行 URL 编码。
* 自动处理来自 Web 应用的 **HTTP** `Set-Cookie` 消息，并重建超时过期会话。这个参数也可以被测试和利用。反之亦然，你可以强制忽略任何 `Set-Cookie` 消息头信息。
* 支持 HTTP **Basic，Digest，NTLM 和 Certificate authentications** 协议。
* **HTTP(S)** 代理支持通过使用验证代理服务器对目标应用发起请求，同时支持 HTTPS 请求。
* 支持伪造 **HTTP** `Referer` 和 **HTTP** `User-Agent`，可通过用户或者从一个文本文件中随机指定。
* 支持设置**输出信息的详细级别**：共有**七个级别**的详细程度。
* 支持从目标 URL 中**解析 HTML 表单**并伪造 HTTP(S) 请求以测试这些表单参数是否存在漏洞。
* 通过用户设置选项调整**粒度和灵活性**。
* 对每一次查询实时**评估完成时间**，使用户能知道输出结果需要的大概时长。
* 在抓取数据时能实时自动保存会话（对应查询和输出结果，支持部分获取保存）到一个文本文件中，并通过解析会话文件**继续当前进行的注入检测**。
* 支持从 INI 配置文件中读取相关配置选项而不是每次都要在命令行中指定。支持从命令行中生成对应的配置文件。
* 支持**复制后端数据库表结构和数据项**到本地的 SQLite 3 数据库中。
* 支持从 SVN 仓库中将 sqlmap 升级到最新的开发版本。
* 支持解析 HTTP(S) 请求响应并显示相关的 DBMS 错误信息。
* 集成其他 IT 安全开源项目，[Metasploit](http://metasploit.com) 和 [w3af](http://w3af.sourceforge.net)。

## 指纹识别和枚举功能

* 基于[错误信息](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html)，[banner 解析](http://bernardodamele.blogspot.com/2007/06/database-management-system-fingerprint.html)，[函数输出对比](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html)和[特定特征](http://bernardodamele.blogspot.com/2007/07/more-on-database-management-system.html)（如 MySQL 注释注入）等方式，收集**大量的数据库版本和底层操作系统指纹信息**。如果你已经知道数据库系统的版本，则可以手动指定它。
* 支持基本 Web 服务器和 Web 应用技术的指纹信息识别技术。
* 支持获取 DBMS **banner**，**会话用户**和**当前数据库**等信息。sqlmap 还能检查当前会话用户是否为数据库管理员（DBA）帐号。
* 支持枚举**用户，密码散列，权限，角色，数据库，数据表和数据列**。
* 支持自动识别密码散列格式并使用**用字典攻击尝试破解**。
* 支持**暴力猜解表名和列名**。当会话用户没有读取系统表的权限或数据库没有存储表结构信息（例如 MySQL &lt; 5.0）时，这项功能会非常有用。
* 支持完整地**导出数据表**，或用户指定的部分数据项、数据列。用户甚至可以只导出部分数据项中的部分字符串。
* 支持自动**导出数据库所有的**数据表和数据实体。在导出时，可以剔除部分指定数据表。
* 支持**搜索指定的数据库名，在数据库中搜索表，或在所有表中搜索列名**。这是一项很有用的功能，例如，可以通过搜索列名中包含像 **name** 和 **pass** 的表来确定哪些表包含用户敏感信息。
* 支持交互式 SQL 客户端功能，用于连接后端数据库并**执行 SQL 语句**。sqlmap 会自动分析用户所提供的 SQL 语句，以此决定用最合适的技术去注入并打包相应的 SQL payload。

## 接管功能

以下的相关技术详细信息可在白皮书[通过高级 SQL 注入完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)和幻灯片[通过数据库进一步控制操作系统](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database)中找到。

* 支持**用户自定义函数注入**：用户可以编译生成共享代码库并通过 sqlmap 在数据库中创建共享库中没有的用户自定义函数。可以通过 sqlmap 执行或者移除这些 UDFs。这些功能当前只支持 MySQL 和 PostgreSQL 数据库。
* 支持从运行 MySQL，PostgreSQL 和 Microsoft SQL Server 的数据库服务器文件系统中**下载和上传文件**。
* 支持在运行 MySQL，PostgreSQL 和 Microsoft SQL Server 的数据库服务器操作系统中**执行任意命令并获取相应输出**。
* 支持在运行 MySQL 和 PostgreSQL 数据库服务器上定义用户自定义函数注入并执行。
* 支持在运行 Microsoft SQL Server 数据库服务器上使用 `xp_cmdshell()` 存储过程。同时，如果注入的存储过程被 DBA 禁用则会被自动启用，在被移除时，则会自动创建。
* 支持在操作系统中**建立攻击者机器和数据库服务器之间的有状态的带外数据 TCP 连接**。根据用户选择这个通信通道可以是交互式命令行，Meterpreter 会话或图形用户界面（VNC）会话。sqlmap 依赖 Metasploit 生成 shellcode ，支持通过四种技术在数据库服务器执行。这些技术分别是：
  * 通过 sqlmap 自带的用户自定义 `sys_bineval()` 函数，**在内存中执行 Metasploit shellcode**。当前支持 MySQL 和 PostgreSQL。
  * 对于 MySQL 和 PostgreSQL，通过 sqlmap 自带的用户自定义 `sys_exec()` 函数上传并执行一个 Metasploit **独立运行的 payload**，对于 Microsoft SQL Server 则使用 `xp_cmdshell()`。
  * 通过 **SMB 反射攻击**（[MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx)）执行 Metasploit shellcode，这需要目标数据库服务器向已被 Metasploit `smb_relay` 监听的攻击者机器发出一个 UNC 路径请求。当 sqlmap 以 Linux/Unix 高权限（`uid=0`）运行，并且目标 DMBS 在 Windows 中以管理员身份运行时支持该功能。
  * 通过利用 **Microsoft SQL Server 2000 和 2005 中存在的 **`sp_replwritetovarbin`** 存储过程堆缓冲区溢出**（[MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)）在内存中执行 Metasploit shellcode。sqlmap 有内置脚本可以自动绕过 DEP 内存保护去触发目标系统漏洞，该脚本依赖 Metasploit ，用于生成 shellcode 以执行攻击。
* 支持通过 Metasploit 的 `getsystem` 命令进行**数据库进程用户提权**，这个命令使用了包括 [kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) 在内等技术（[MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx)）。
* 支持访问（读取/添加/删除）Windows 注册表配置单元。

## 演示

你可以在 [Bernardo](http://www.youtube.com/user/inquisb/videos) 和 [Miroslav](http://www.youtube.com/user/stamparm/videos) 的 YouTube 主页中观看演示。还可以在 [这里](http://unconciousmind.blogspot.com/search/label/sqlmap) 找到大量含有公开可用漏洞的 Web 应用，用于合法的 Web 应用安全评估。
