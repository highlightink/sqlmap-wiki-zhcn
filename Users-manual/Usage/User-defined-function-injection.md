## 用户自定义函数注入

以下选项用于创建用户自定义函数。

### 注入用户自定义函数（UDF）

开关和选项：`--udf-inject` 和 `--shared-lib`

你可以通过编译 MySQL 或 PostgreSQL 共享库（在 Windows 上为 DLL，在 Linux/Unix 上为共享对象（shared object））来注入自己的用户自定义函数（UDFs），然后将本地存储共享库的目录路径提供给 sqlmap。sqlmap 会根据你的选择决定下一步是向数据库服务器文件系统上传共享库到还是创建用户自定义函数。当你完成注入 UDFs 的使用后，sqlmap 还可以将它们从数据库中删除。

这些技术在白皮书[通过高级 SQL 注入完全控制操作系统](http://www.slideshare.net/inquis/advanced-sql-injection-to-operating-system-full-control-whitepaper-4633857)中有详细介绍。

使用选项 `--udf-inject` 并按照说明进行操作即可。

如果需要，也可以使用 `--shared-lib` 选项通过命令行指定共享库的本地文件系统路径。否则 sqlmap 会在运行时向你询问路径。

此功能仅在 DBMS 为 MySQL 或 PostgreSQL 时可用。