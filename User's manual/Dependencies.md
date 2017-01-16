# 相关依赖

sqlmap 使用 [Python](http://www.python.org) 编写的, Python 是一门动态的、面向对象的解析型语言，你可以从 [http://python.org/download/](http://python.org/download/) 上面免费下载安装. 因而 sqlmap 独立于操作系统，是一款跨平台的工具。使用 sqlmap 你需要 Python **2.6.x** 或者 **2.7.x** 版本的相关环境。不过，通常 GNU/Linux 下的发行版本都会预安装好相关的 python 版本。其他的 Unix 和 Mac OSX 通常都会有相关的 Python 依赖包，并且可以很容易地进行相关安装。Windows 用户可以针对 x86、 AMD64 和 Itanium 选择不同的安装程序。

sqlmap 的部分漏洞利用功能依赖于 [Metasploit 框架](http://metasploit.com). 你可以从 [这里](http://metasploit.com/download/) 获取 Metasploit 框架，版本要求是 **3.5** 或者更高版本。对于 ICMP 隧道入侵技术，sqlmap 需要 [Impacket](https://code.google.com/p/impacket/) 依赖包.

如果你想要绕过 Web 应用，直接连接到数据库服务器（开启 `-d` 选项），你需要根据攻击目标中不同数据库管理系统安装不同的 Python 连接依赖包：

* DB2: [python ibm-db](https://code.google.com/p/ibm-db/)
* Firebird: [python-kinterbasdb](http://kinterbasdb.sourceforge.net/)
* Microsoft Access: [python-pyodbc](https://code.google.com/p/pyodbc/)
* Microsoft SQL Server: [python-pymssql](http://code.google.com/p/pymssql/)
* MySQL: [python pymysql](https://github.com/PyMySQL/PyMySQL/)
* Oracle: [python cx_Oracle](http://cx-oracle.sourceforge.net/)
* PostgreSQL: [python-psycopg2](http://initd.org/psycopg/)
* SQLite: [python-pysqlite2](https://code.google.com/p/pysqlite/)
* Sybase: [python-pymssql](http://code.google.com/p/pymssql/)

如果你想要攻击部署了 NTLM 验证的 Web 应用，那么你需要安装 [python-ntlm](http://code.google.com/p/python-ntlm/) 依赖包。
 
此外，如果你是在 Windows 中运行 sqlmap，为了更好地支持 SQL shell 和 OS shell 中 sqlmap TAB 自动补全功能和历史记录功能，那么你可能会想要安装 [PyReadline](http://ipython.scipy.org/moin/PyReadline/Intro) 依赖包。需要注意的是，在其他系统中，这些功能都是由标准 Python 依赖包 [readline](http://docs.python.org/library/readline.html) 支持的。

# Dependencies

sqlmap is developed in [Python](http://www.python.org), a dynamic, object-oriented, interpreted programming language freely available from [http://python.org/download/](http://python.org/download/). This makes sqlmap a cross-platform application which is independant of the operating system. sqlmap requires Python version **2.6.x** or **2.7.x**. To make it even easier, many GNU/Linux distributions come out of the box with Python installed. Other Unixes and Mac OSX also provide Python packaged and ready to be installed. Windows users can download and install the Python installer for x86, AMD64 and Itanium.

sqlmap relies on the [Metasploit Framework](http://metasploit.com) for some of its post-exploitation takeover features. You can grab a copy of the framework from the [download](http://metasploit.com/download/) page - the required version is **3.5** or higher. For the ICMP tunneling out-of-band takeover technique, sqlmap requires the [Impacket](https://code.google.com/p/impacket/) library too.

If you are willing to connect directly to a database server (switch `-d`), without passing through the web application, you need to install Python bindings for the database management system that you are going to attack:

* DB2: [python ibm-db](https://code.google.com/p/ibm-db/)
* Firebird: [python-kinterbasdb](http://kinterbasdb.sourceforge.net/)
* Microsoft Access: [python-pyodbc](https://code.google.com/p/pyodbc/)
* Microsoft SQL Server: [python-pymssql](http://code.google.com/p/pymssql/)
* MySQL: [python pymysql](https://github.com/PyMySQL/PyMySQL/)
* Oracle: [python cx_Oracle](http://cx-oracle.sourceforge.net/)
* PostgreSQL: [python-psycopg2](http://initd.org/psycopg/)
* SQLite: [python-pysqlite2](https://code.google.com/p/pysqlite/)
* Sybase: [python-pymssql](http://code.google.com/p/pymssql/)

If you plan to attack a web application behind a NTLM authentication you'll need to install [python-ntlm](http://code.google.com/p/python-ntlm/) library.

Optionally, if you are running sqlmap on Windows, you may wish to install the [PyReadline](http://ipython.scipy.org/moin/PyReadline/Intro) library in order to take advantage of the sqlmap TAB completion and history support features in the SQL shell and OS shell. Note that these functionalities are available natively via the standard Python [readline](http://docs.python.org/library/readline.html) library on other operating systems.

