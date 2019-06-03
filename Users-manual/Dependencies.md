# 相关依赖

sqlmap 使用 [Python](http://www.python.org) 开发，Python 是一门动态的、面向对象的解释型语言，你可以从 [http://python.org/download/](http://python.org/download/) 上面免费下载安装。因而 sqlmap 独立于操作系统，是一款跨平台的工具。使用 sqlmap 需要 Python **2.6**，**2.7** 或 **3.x** 的相关环境。通常 GNU/Linux 的发行版都会预安装好相关的 Python 版本。其他的 Unix 和 Mac OSX 通常也会有相关的 Python 依赖包，并且可以很容易地进行安装。Windows 用户可以针对 x86，AMD64 和 Itanium 选择不同的安装程序。

sqlmap 的部分漏洞利用功能依赖于 [Metasploit 框架](http://metasploit.com)。你可以从[这里](http://metasploit.com/download/)获取 Metasploit 框架，版本要求是 **3.5** 或者更高版本。对于 ICMP 隧道入侵技术，sqlmap 需要 [Impacket](https://code.google.com/p/impacket/) 依赖包。

如果你想要绕过 Web 应用，直接连接到数据库服务器（开启 `-d` 选项），你需要根据攻击目标中不同数据库管理系统安装不同的 Python 连接依赖包：

* DB2：[python ibm-db](https://code.google.com/p/ibm-db/)
* Firebird：[python-kinterbasdb](http://kinterbasdb.sourceforge.net/)
* Microsoft Access：[python-pyodbc](https://code.google.com/p/pyodbc/)
* Microsoft SQL Server：[python-pymssql](http://code.google.com/p/pymssql/)
* MySQL：[python pymysql](https://github.com/PyMySQL/PyMySQL/)
* Oracle：[python cx_Oracle](http://cx-oracle.sourceforge.net/)
* PostgreSQL：[python-psycopg2](http://initd.org/psycopg/)
* SQLite：[python-pysqlite2](https://code.google.com/p/pysqlite/)
* Sybase：[python-pymssql](http://code.google.com/p/pymssql/)

如果你想要攻击部署了 NTLM 验证的 Web 应用，那么你需要安装 [python-ntlm](http://code.google.com/p/python-ntlm/) 依赖包。
 
此外，如果你是在 Windows 中运行 sqlmap，为了更好地支持 SQL shell 和 OS shell 中 sqlmap TAB 自动补全功能和历史记录功能，那么你可能会想要安装 [PyReadline](http://ipython.scipy.org/moin/PyReadline/Intro) 依赖包。需要注意的是，在其他系统中，这些功能都是由标准 Python 依赖包 [readline](http://docs.python.org/library/readline.html) 支持的。
