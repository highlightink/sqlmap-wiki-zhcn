# 第三方库

> *译自：[Third party libraries](https://github.com/sqlmapproject/sqlmap/wiki/Third-party-libraries)*

## `thirdparty/` 目录中的第三方库

| 库 | 许可证 | 备注 | 开关 |
| ------------ | ----------- | ----------- | ----------- |
| [thirdparty/ansistrm/](http://plumberjack.blogspot.co.uk/2010/12/colorizing-logging-output-in-terminals.html) | BSD | 用于着色日志消息 | - |
| [thirdparty/beautifulsoup/](http://www.crummy.com/software/BeautifulSoup/) | BSD | 用于爬取目标站点 | --crawl |
| [thirdparty/bottle/](http://bottlepy.org/) | MIT | 用作 RESTful API 的微型 Web 服务器 | sqlmapapi.py |
| [thirdparty/chardet/](http://pypi.python.org/pypi/chardet) | LGPL | 用于启发式检测 HTTP 响应体字符集 | - |
| [thirdparty/clientform/](http://wwwsearch.sourceforge.net/old/ClientForm/) | BSD | 用于解析 HTML 表单 | --forms |
| [thirdparty/colorama/](http://pypi.python.org/pypi/colorama) | BSD | 用于跨平台着色输出 | - |
| [thirdparty/fcrypt/](http://carey.geek.nz/code/python-fcrypt/) | BSD | 用于破解通用密码哈希 | --passwords |
| [thirdparty/gprof2dot/](http://code.google.com/p/jrfonseca/wiki/Gprof2Dot) | LGPL | 用于内部调试目的 | --profile |
| [thirdparty/identywaf/](https://github.com/stamparm/identYwaf) | MIT | 用于识别 WAF/IPS 解决方案 | - |
| [thirdparty/keepalive/](http://urlgrabber.baseurl.org/) | LGPL | 用于持久的 HTTP(s) 请求 | --keep-alive and -o |
| [thirdparty/magic/](http://pypi.python.org/pypi/python-magic/) | PSF | 用于识别和显示日志消息中的文件类型 | --file-write |
| [thirdparty/multipartpost/](http://pipe.scs.fsu.edu/PostHandler/MultipartPostHandler.py) | LGPL | 用于通过 Web 文件传输器上传文件 | --os-cmd, --os-shell, --os-pwn |
| [thirdparty/odict/](http://www.voidspace.org.uk/python/odict.html) | BSD | 内部使用 | - |
| [thirdparty/pagerank/](http://code.google.com/p/corey-projects/) | MIT | 用于显示 Google dork 结果的页面排名 | -g |
| [thirdparty/prettyprint/](http://code.google.com/p/python-httpclient-gui/) | MIT | 用于生成 XML 输出 | --xml, to be replaced by --report (#14) |
| [thirdparty/pydes/](http://twhiteman.netfirms.com/des.html) | Free, public domain | 用于破解 Oracle 旧密码格式 | --passwords |
| [thirdparty/six/](https://github.com/benjaminp/six) | MIT | 用于 Python 2 与 3 的兼容 | - |
| [thirdparty/socks/](http://socksipy.sourceforge.net/) | BSD | 用于通过 Tor SOCKS 代理隧道传输请求 | --tor-type and --proxy |
| [thirdparty/termcolor/](http://pypi.python.org/pypi/termcolor) | MIT | 用于着色输出 | - |
| [thirdparty/xdot/](http://code.google.com/p/jrfonseca/wiki/XDot) | LGPL | 用于内部调试目的 | --profile |

## extra/ 目录中的第三方库与工具

这些列出的为不是完全由 sqlmap 开发人员开发的库和工具。

| 库 / 工具 | 许可证 | 备注 | 开关 |
| ------------ | ----------- | ----------- | ----------- |
| [extra/icmpsh/](https://github.com/inquisb/icmpsh) | LGPL | 用于通过 ICMP 接管操作系统的功能 | --os-pwn |

## 未打包的依赖

| 库 / 工具 | 许可证 | 备注 | 开关 |
| ------------ | ----------- | ----------- | ----------- |
| [Metasploit Framework](http://www.metasploit.com) | BSD | 用于操作系统接管功能 | --os-pwn, --os-bof, --os-smbshell |
| [PyReadline](http://ipython.scipy.org/moin/PyReadline/Intro) | BSD | 用于 TAB 自动补全和历史记录 | --os-shell and --sql-shell |
| [python cx_Oracle](http://cx-oracle.sourceforge.net/) | BSD | Oracle 连接器 | -d |
| [python ibm-db](https://code.google.com/p/ibm-db/) | Apache | DB2 连接器 | -d |
| [python-impacket](http://code.google.com/p/impacket/) | BSD | 用于通过 icmpsh 的操作系统接管功能 | --os-pwn |
| [python-kinterbasdb](http://kinterbasdb.sourceforge.net/) | BSD | Firebird 连接器 | -d |
| [python-ntlm](http://code.google.com/p/python-ntlm/) | LGPL | 用于站点需要 NTLM 身份验证时 | --auth-type |
| [python-psycopg2](http://initd.org/psycopg/) | LGPL | PostgreSQL 连接器 | -d |
| [python-pyodbc](https://code.google.com/p/pyodbc/) | MIT | Microsoft Access 连接器 | -d |
| [python-pymssql](http://pymssql.sourceforge.net/) | LGPL | Microsoft SQL Server 连接器 | -d |
| [python pymysql](http://code.google.com/p/pymysql/) | MIT | MySQL 连接器 | -d |
| [python-pysqlite2](https://code.google.com/p/pysqlite/) | MIT | SQLite 2 连接器 | -d |
