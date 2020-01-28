# 访问 Windows 注册表

当后端 DBMS（Database Management System，数据库管理系统）是 MySQL，PostgreSQL 或 Microsoft SQL Server 并且 Web 应用程序支持堆叠查询时，sqlmap 可以访问 Windows 注册表。此外，会话用户必须具备相应的访问权限。

## 读取 Windows 注册表键值

开关：`--reg-read`

使用此开关可以读取注册表键值。

## 写入 Windows 注册表键值

开关：`--reg-add`

使用此开关可以写入注册表键值。

## 删除 Windows 注册表项

开关：`--reg-del`

使用此开关可以删除注册表项。

## 注册表辅助选项

选项：`--reg-key`，`--reg-value`，`--reg-data` 和 `--reg-type`

这些选项用于提供正确运行 `--reg-read`，`--reg-add` 和 `--reg-del` 等开关所需的数据。因此，你可以在命令提示符下使用它们作为程序参数，而不是在执行过程中提供注册表项信息。

使用 `--reg-key` 选项指定 Windows 注册表项路径，`--reg-value` 提供注册表项的名称，`--reg-data` 提供注册表键值数据，而 `--reg-type` 选项指定注册表键值的类型。

添加注册表项配置单元的示例命令行如下：

```shell
$ python sqlmap.py -u http://192.168.136.129/sqlmap/pgsql/get_int.aspx?id=1 --r\
eg-add --reg-key="HKEY_LOCAL_MACHINE\SOFTWARE\sqlmap" --reg-value=Test --reg-ty\
pe=REG_SZ --reg-data=1
```
